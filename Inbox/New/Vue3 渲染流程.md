---
title: Vue3 渲染流程
date created: 2023-08-20
date modified: 2023-08-20
---

## app.mount时vue做了些什么

首先创建一个App

```javascript
import { createApp } from 'vue';
import App from './App.vue';
const app = createApp(App);
```

这个 `createApp` 是由 `createAppAPI` 返回的高阶函数：

```javascript
function createAppAPI<HostElement>(
  render: RootRenderFunction,
  hydrate?: RootHydrateFunction
): CreateAppFunction<HostElement> {
	return function createApp(rootComponent, rootProps = null) {...}
}
```

`render` 是渲染实现，`vue` 默认使用 `@vue/runtime-dom` 中浏览器的渲染实现

> [!NOTE]
> 
> 从这里可以看出，vue3 分离了渲染器，这也让其拥有了作为跨平台框架的能力

`createApp` 最后会返回一个 `Vue` 实例，我们将会调用其 `mount` 方法渲染到指定元素上

```javascript
app.mount('#app');
```

在 `mount` 中，先创建 `vnode` 节点

```javascript
const vnode = createVNode(
	rootComponent as ConcreteComponent,
	rootProps
)
```

创建 `vnode` 节点后，就是渲染

```javascript

```
