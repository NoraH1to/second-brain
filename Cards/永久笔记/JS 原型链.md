---
title: JS 原型链
date created: 2023-08-18
date modified: 2023-08-19
---

> 原文：[原型链 | 乱炖锅 (norah1to.com)](https://www.norah1to.com/2022/06/07/basic/javascript/prototype-chain/)

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

## 原型链

使用 `__proto__` 串起原型，就是原型链了，顶端即 `Object.prototype.__proto__`，为 `null`

## instanceof

`instanceof` 用于判断对象类型，其机制正是在原型链上寻找原型：

- `target.__proto__`
- `target.__proto__.__proto__`
- 以此类推

## 所有对象都是 Object 类型

```javascript
function Foo() {};
Array instanceof Object; // true
Function instanceof Object; // true
Foo instanceof Object; // true
```

因为函数对象 `Function` 是 `Object` 的派生实例：

```javascript
Function.__proto__.__proto__ === Object.prototype; // true
```

且实例都派生自 `Function`，那么肯定也派生自 `Object`

## 实例本身不存在的属性，会尝试在原型上找

当我们访问实例属性时，如果实例上没找到，会在构造实例的函数对象的原型上找

下面的 `foo` 实例本身并没有 `toString`，但是读取时可以读到，这是因为 `Object.prototype` 上有这个属性

```javascript
function Foo() {}
var foo = new Foo();
Object.hasOwn(foo, 'toString'); // false
foo.toString // ƒ toString() { [native code] }
```

具体查找逻辑就是沿着原型链一路往上找

`foo.__proto__` -> `foo.__proto__.__proto__` -> ... -> `Object.prototype.__proto__`

## 经典老图

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230818171845.png)