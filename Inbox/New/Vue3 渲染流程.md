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
render(vnode, rootContainer, isSVG)
```

`render` 就是官方的浏览器渲染实现，其内部实现如下

```javascript
const render: RootRenderFunction = (vnode, container, isSVG) => {
	if (vnode == null) {
		if (container._vnode) {
			unmount(container._vnode, null, null, true)
		}
	} else {
		patch(container._vnode || null, vnode, container, null, null, null, isSVG)
	}
	flushPostFlushCbs()
	container._vnode = vnode
}
```

- 如果节点为 `null` ，则将节点从当前容器 dom 上卸载
- 否则就进行 `patch`（打补丁）
- 最后执行 `Post` 队列中的任务

> [!NOTE] Post 队列
> 
> [`watchEffect`](https://cn.vuejs.org/api/reactivity-core.html#watcheffect) 中的 `flush` 会指定响应依赖的执行时机，`Post` 队列中的任务需要在选然后执行

关键就是这里的 `patch`，在这里面会判断 `vnode` 的类型

```javascript
switch (type) {
	case Text:
		...
		break
	case Comment:
		...
		break
	case Static:
		...
		break
	case Fragment:
		...
		break
	default:
		...
```

由于我们还只是抽象的组件，还未渲染，这里会走到 `default` 中，在这里面判断我们传入的是组件后，会调用 `processComponent` 方法：

```javascript
if (shapeFlag & ShapeFlags.ELEMENT) {
	...
} else if (shapeFlag & ShapeFlags.COMPONENT) {
	processComponent(...)
} else {
	...
}
```

接着在里面判断是初次渲染后，会接着调用 `mountComponent` 方法，在里面创建组件实例

```javascript
const instance: ComponentInternalInstance =
	compatMountInstance ||
	(initialVNode.component = createComponentInstance(
		initialVNode,
		parentComponent,
		parentSuspense
	))
```

在 `mountComponent` 中会调用 `setupComponent` 方法，该方法中进行了很多初始化操作

- 初始化 `props`
- 初始化插槽
- 执行 `setup` 函数
- 根据 `setup` 返回值或者模板属性，编译 `render` 渲染函数

执行完 `setupComponent` 后，走到接收该实例的 `setupRenderEffect` 方法

里面定义了组件渲染、更新的方法 `componentUpdateFn`，该方法由两百多行，非常的重要，Vue 使用该方法在这里新建了一个响应式副作用，双向绑定的魔法在此展开

```javascript
// create reactive effect for rendering
const effect = (instance.effect = new ReactiveEffect(
	componentUpdateFn,
	() => queueJob(update),
	instance.scope // track it in component's effect scope
))
const update: SchedulerJob = (instance.update = () => effect.run())
update.id = instance.uid
update() // 执行渲染，渲染过程中使用的响应式变量都会被计入依赖
```

在 `componentUpdateFn` 中，如果我们是首次渲染，则会调用 `renderComponentRoot` 构建 `vnode` 树

```javascript
const subTree = (instance.subTree = renderComponentRoot(instance))
```

然后执行 `patch` 将 `vnode` 渲染到浏览器中，完成了首屏渲染