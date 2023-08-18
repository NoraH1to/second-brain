---
title: JS 事件循环
date created: 2023-08-17
date modified: 2023-08-18
---
## 事件循环

js 是单线程语言，浏览器中 js 主线程会阻塞渲染，因此需要将一些耗时的任务放到别的线程去作为异步任务执行

浏览器为了在主线程中能够协调各种异步事件（网络、用户操作），引入了事件循环模型

在事件循环中，我们将待执行的任务分为两大类，有两个对应的队列负责存放这两种任务：

- **宏任务**：setTimeout、setInterval、setImmediate、requestAnimationFrame、I/O、UI 渲染、用户交互
- **微任务**：process.nextTick、Promise、MutationObserver、[queueMicrotask](https://developer.mozilla.org/zh-CN/docs/Web/API/queueMicrotask)

事件循环大致流程：

1. 浏览器在执行我们代码的过程中，会将生成的任务加入其对应的队列
2. 代码执行完毕后（[[#调用栈]]为空），循环取出**微任务队列**中**最早**的任务并执行，直到该队列为空
3. 取出**一个宏任务队列**中**最早**的任务并执行，回到第二步

> [!NOTE] 任务执行
> 任务执行的时候也能产生任务，如果递归执行微任务，会阻塞整个页面

## 调用栈

js 使用栈先进后出的特性来保证代码的执行顺序，例如下面代码：

```javascript
function foo() {
	console.log('foo');
}
function bar() {
	foo();
	console.log('bar');
}
bar();
```

1. 执行 `bar()`，入栈：`[bar()]`
2. 执行 `bar` 中的 `foo()`，入栈：`[bar(), foo()]`
3. 执行 `foo` 中的 `console.log('foo')`，入栈：`[bar(), foo(), console.log('foo')]`
4. `console.log('foo')` 执行完毕，出栈：`[bar(), foo()]`
5. `foo()` 执行完毕，出栈：`[bar()]`
6. 执行 `foo` 中的 `console.log('bar')`，入栈：`[bar(), console.log('bar')]`
7. `console.log('bar')` 执行完毕，出栈：`[bar()]`
8. `bar()` 执行完毕，出栈：`[]`

具体可以看这个[在线例子](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gZm9vKCkgewoJY29uc29sZS5sb2coJ2ZvbycpOwp9CmZ1bmN0aW9uIGJhcigpIHsKCWZvbygpOwoJY29uc29sZS5sb2coJ2JhcicpOwp9CmJhcigpOw%3D%3D!!!)