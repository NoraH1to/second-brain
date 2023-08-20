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
