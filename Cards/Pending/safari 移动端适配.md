---
title: safari 移动端适配
date created: 04/18-2023, 14:35, 星期二
date modified: 06/09-2023, 15:45, 星期五
---

最近在做一个 web 端的阅读器，缘由暂且不提，适配 SM （移动端 Safari） 时简直要了 👴 的小命！

## 浏览器高度

首先是浏览器高度，在 SM 中，`vh` 在计算时会包括顶部的导航栏

![圈中的部位，包括系统状态栏的高度也会计算](./vh_include_nav.png)

也就是说，如果你想让内容填满整个可视区域，用 `height: 100vh` 会溢出一大截，解决的方式很简单，用 `height: 100%` 或者别的方式替代（比如 `flex` 布局）

### 为什么

在我们滚动页面的时候，导航栏可以透过毛玻璃看到滚动到后面的内容，所以 SM 的实际可视区域还包括了导航栏

![状态栏毛玻璃](./nav_render.gif)

经过测试，iOS 平台的 Chrome 也能复现高度问题，可以推测出 iOS 平台上的浏览器都需要注意这点，因为 iOS 限制了第三方浏览器只能使用系统内置的 `WebKit` 内核

## 上下文菜单

这个问题就比较重量级了，通过触屏长按目标触发 `contextmenu` 事件在 SM 上直接不支持

![不支持 contextmenu](./contextmenu_not_work.gif)

从图中也可以看出，应该是为了 iOS 中的[跨应用拽托功能](https://zhuanlan.zhihu.com/p/429768878)做出了让步（在以前是为[无障碍功能-放大镜](https://bugs.webkit.org/show_bug.cgi?id=213953)让步）

一开始我不信邪，用 Chat-GPT 查了一圈上下文菜单的库，例如 [`react-contexify`](https://github.com/fkhadra/react-contexify)、[`react-menu`](https://github.com/szhsin/react-menu)，他们都没有处理这个问题

最后找到 [`Quasar`](https://quasar.dev/vue-components/menu#context-menu) 和 [`radix-ui`](https://www.radix-ui.com/docs/primitives/components/context-menu) 做了处理（还得是知名的组件库），但方法并不出彩，是我已有的思路：监听触控事件时长来判断是否显示菜单

算了，又不是不能用，最后我在 `touch` 和 `pointer` 事件中选择 `pointer` 来实现，理由很简单，它兼容性更好

![一片绿](./pointer_enable.png)

最后贴一下 `react` 中的实现，先是一个 hook

```typescript
// useContextMenu.ts
import { useCallback, useEffect, useRef, useState } from 'react';

function whenTouchOrPen<E>(
  handler: React.PointerEventHandler<E>
): React.PointerEventHandler<E> {
  return (event) =>
    event.pointerType !== 'mouse' ? handler(event) : undefined;
}

const useContextMenu = <
  T extends React.DOMAttributes<HTMLElement> = React.DOMAttributes<HTMLElement>
>(
  onOpen?: (position: { x: number; y: number }) => void
) => {
  const [disabled, setDisabled] = useState(false);
  const position = useRef({ x: -999, y: -999 });

  useEffect(() => {
    if (disabled) clearLongPress();
  }, [disabled]);

  const handleOpen = (e: React.MouseEvent | React.PointerEvent) => {
    position.current = { x: e.clientX, y: e.clientY };
    onOpen?.(position.current);
  };

  const longPressTimer = useRef(0);
  const clearLongPress = useCallback(() => {
    window.clearTimeout(longPressTimer.current);
  }, []);

  const onContextMenu: T['onContextMenu'] = disabled
    ? undefined
    : (e) => {
        // 大部分触控平台都支持长按触发 `contextmenu` 事件，清除计时器
        clearLongPress();
        handleOpen(e);
        e.preventDefault();
      };

  const onPointerDown: T['onPointerDown'] = disabled
    ? undefined
    : whenTouchOrPen((e) => {
        clearLongPress();
        // 在触发 iOS 跨应用拽托前响应，500ms 比较合适
        longPressTimer.current = window.setTimeout(() => handleOpen(e), 500);
      });

  const onPointerMove: T['onPointerMove'] = disabled
    ? undefined
    : whenTouchOrPen(clearLongPress);

  const onPointerCancel: T['onPointerCancel'] = disabled
    ? undefined
    : whenTouchOrPen(clearLongPress);

  const onPointerUp: T['onPointerUp'] = disabled
    ? undefined
    : whenTouchOrPen(clearLongPress);

  return [
    {
      triggerProps: {
        onContextMenu,
        onPointerDown,
        onPointerMove,
        onPointerCancel,
        onPointerUp,
      },
      position: position.current,
    },
    { setDisabled },
  ] as const;
};

export default useContextMenu;
```

然后是一个利用该 hook 封装的高阶组件：

```tsx
// ContextMenu.tsx
import useContextMenu from '@/hooks/useContextMenu';

import type { FC } from 'react';

const ContextMenu: FC<{
  onOpen?: Parameters<typeof useContextMenu>[0];
  children:
    | React.ReactNode
    | ((...props: ReturnType<typeof useContextMenu>) => React.ReactNode);
}> = (props) => {
  const { onOpen, children } = props;
  const hookRes = useContextMenu(onOpen);
  return (
    <>{typeof children === 'function' ? children(...hookRes) : children}</>
  );
};

export default ContextMenu;
```

## 不可靠的 BLOB

> SM 存放在 `indexedDB`（web 浏览器数据库）的 `blob` 并不可靠

在页面间来回跳转的时候，`blob` 引用中的数据会**莫名其妙的消失**

为了让 `blob` 更可靠，我们在做持久化的时候，需要抽取 `blob` 中的 `arrayBuffer` 和 `type`，使用的时候再组装

简单写个伪代码例子

```typescript
// 存储
const book; // Blob
fs.saveBlob('一本书', {
  arrayBuffer: await book.arrayBuffer(),
  type: book.type,
});
// 使用
const origin = fs.getBlob('一本书');
const book = new Blob([origin.arrayBuffer], { type: origin.type });
```

### 为什么

从 iOS-13 和 Safari-13.1 开始，indexedDB 的数据**不再具有持久性**

> 参考：
>
> [Error loading Blob to img in Safari](https://stackoverflow.com/a/70253220)
>
> [Is IndexedDB on Safari guaranteed to be persistent?](https://stackoverflow.com/a/69722364)

## 子元素溢出

> 子元素应用 CSS3 动画属性后，会无视祖先元素的 `border-radius` 溢出

举个例子，当你在写如下页面时

<iframe height="300" style="width: 100%;" scrolling="no" title="safari child overflow" src="https://codepen.io/norah1to/embed/wvYEoGg?default-tab=js%2Cresult&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/norah1to/pen/wvYEoGg">
  safari child overflow</a> by NoraH1to (<a href="https://codepen.io/norah1to">@norah1to</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>

你会不会觉得，“诶，完全没问题啊？”

但是

![在 SM 中的实际表现](./child_overflow.png)

我圆角呢？我不到啊？

此时我们需要在其声明了 `overflow: hidden` 和 `border-radius` 的元素上增加一个样式

```css
.rounded-wrapper {
  ...
  isolation: isolate;
}
```

![我好了，你呢](./child_overflow_fix.png)

### 为什么

这是一个 `webkit` 的 BUG，所以没有为什么

解决思路主要是让其通过其它方式计算溢出，`isolation: isolate` 可以新建一个独立于其它部分的层叠上下文，自然就触发了别的计算方式

同理 `transform` 也是可行的，但是有缺陷（阴影失效等等），不推荐使用

> 参考：
>
> [Overflow: hidden with border radius not working on Safari](https://stackoverflow.com/a/58283449)
>
> [overflow: hidden + border radius does not work when transform is added to child](https://bugs.webkit.org/show_bug.cgi?id=98538)

## 最后

我要感谢 safari，让我在 2023 年依旧可以体验到前辈们适配 IE 的痛苦，我谢谢你啊 🥰

## 未完待续
