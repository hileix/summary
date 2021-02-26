# JS 继承

<img src="../../思维导图/JS继承.png">

## 1. 组合式继承

```js
// 父类
function Super(name) {
  // 父类属性
  this.name = name;
}
// 父类方法
Super.prototype.sayName = function () {
  console.log('name');
};

// 子类继承父类
function Sub(name, age) {
  // 继承属性
  Super.apply(this, name);
  // 子类属性
  this.age = age;
}
// 子类通过原型链继承方法
Sub.prototype = new Super();
Sub.prototype.constructor = Sub;
```

缺点：需要调用两次构造函数，会影响性能。

## 2. 寄生组合式继承

寄生组合式继承，就是为了解决组合式继承的缺点的。

```js
// 父类
function Super(name) {
  // 父类属性
  this.name = name;
}
// 父类方法
Super.prototype.sayName = function () {
  console.log('name');
};

// 关键方法：用于继承父类方法的函数。同 Obect.create()
function objectCreate(prototype) {
  var F = function () {};
  F.prototype = prototype;
  return new F();
}
// 子类
function Sub(name, age) {
  // 继承属性
  Super.call(this, name);
}

// 继承方法
function inheritPrototype(Super, Sub) {
  Sub.prototype = objectCreate(Super.prototype);
  Sub.prototype.constructor = Sub;
}
inheritPrototype(Super, Sub);
```
