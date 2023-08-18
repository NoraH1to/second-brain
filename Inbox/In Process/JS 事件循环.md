## 调用栈

js 使用栈来保证代码的执行顺序，例如下面代码：

```javascript
function foo() {
	console.log('foo');
};
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
6. 执行 `foo` 中的 `cons`