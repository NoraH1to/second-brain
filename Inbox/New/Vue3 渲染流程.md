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

`render` 是渲染实现
