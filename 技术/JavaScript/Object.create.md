# Object.create

`Object.create(proto, [propertiesObject])` 方法会创建一个 `新的空的对象`，传入的 `obj` 会作为 `新的空的对象` 的原型。

```js
var o1 = { a: 1 };

var o2 = Object.create(o1); // {}

o2.__proto__; // { a: 1 }

o1 === o2.__proto__; // true
```

第二个参数 `propertiesObject` 可以指定要添加到新对象的可枚举属性：

```js
var o1 = Object.create(null, {
  a: {
    writable: true,
    configurable: true,
    value: 1,
  },
});
o1; // { a: 1 }
```

## 1. 返回一个没有原型的对象

当传入的是 `null` 时，返回的 `新对象` 就没有原型了。

```js
var o1 = Object.create(null); // {}
o1.__proto__; // undefined
```

## 2. Object.create() 方法的实现

在 [JS 继承](./JS继承.md) 中，可以找到该方法。

```js
function objectCreate(obj) {
  var F = function () {};
  F.prototype = obj;
  return new F();
}
```

## 3. 使用场景

- 使用 `Object.create(null)` 创建一个没有原型的纯净的对象，可以在 `for...in` 循环中移除 `hasOwnProperty` 判断，提高性能。
