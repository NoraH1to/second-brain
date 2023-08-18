---
title: JS 原型链
date created: 2023-08-18
date modified: 2023-08-18
---

## 所有东西都是对象

在 JS 中，除了 `null`, `undefined` 以外**所有数据类型**都是对象

对象分为[[#普通对象]]和[[#函数对象]]

### 普通对象

就是我们常识中的对象，所有普通对象都持有 `__proto__` 属性，该属性指向**构建该实例的函数对象的原型**

常见的普通对象如下：

- `{}` 字面量对象，`Object` 的实例
```javascript
({}).__proto__ === Object.prototype; // true
```
- `foo = new Foo()`，`foo` 就是普通对象，是 `Foo` 的实例
```javascript
function Foo() {}
new Foo().__proto__ === Foo.prototype; // true
```
- `"abc"`，`String` 的实例
```javascript

```