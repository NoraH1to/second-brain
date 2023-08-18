---
title: JS 原型链
date created: 2023-08-18
date modified: 2023-08-18
---

## 所有东西都是对象

在 JS 中，除了 `null`, `undefined` 以外**所有数据类型**都是对象

对象分为[[#普通对象]]和[[#函数对象]]

### 普通对象

就是我们常识中的对象，常见的普通对象如下：

- 字面量对象 `{}`
- 字面量字符串 `"abc"`
- 字面量数值 `123`
- `NaN`
- `foo = new Foo()`，`foo` 就是普通对象

### 函数对象

函数就是函数对象

```javascript
function Foo() {}
```

`Foo` 就是一个函数对象

函数对象同时也是普通对象，**函数对象是普通对象的超集**，特点之一就是拥有**原型** `prototype`

## 普通对象是函数对象的实例

所有普通对象都持有 `__proto__` 属性，该属性指向**构建该实例的函数对象的原型**

以[[#普通对象]]里的例子为例子：

- 字面量对象 `{}`，`Object` 的实例
```javascript
({}).__proto__ === Object.prototype; // true
```
- 字面量字符串 `"abc"`，`String` 的实例
```javascript
"abc".__proto__ === String.prototype; // true
```
- 字面量数值 `123`，`Number` 的实例（`NaN` 也是）
```javascript
(123).__proto__ === Number.prototype; // true
NaN.__proto__ === Number.prototype; // true
```
- `foo = new Foo()`，`foo` 就是普通对象，是 `Foo` 的实例
```javascript
function Foo() {}
new Foo().__proto__ === Foo.prototype; // true
```

## 所有函数对象都是 `Function` 的实例

`Function` 本身也是函数对象，所以它是它自身的实例

```javascript
function Foo() {}
Foo.__proto__ === Function.prototype; // true
String.__proto__ === Function.prototype; //true
Object.__proto__ === Function.prototype; //true
Function.__proto__ === Function.prototype; // true
```

## 所有原型都是 `Object` 的实例

```javascript
function Foo() {}
Foo.prototype.__proto__ === Object.prototype; // true
String.prototype.__proto__ === Object.prototype; // true
Function.prototype.__proto__ === Object.prototype; // true
```

但 `Object` 自身的原型并不是任何函数对象的实例，因此

```javascript
Object.prototype.__proto__; // null
```



