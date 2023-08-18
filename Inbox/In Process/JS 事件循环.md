---
title: JS 事件循环
date created: 2023-08-17
date modified: 2023-08-18
---
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

## 事件循环