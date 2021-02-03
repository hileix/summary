# js 的继承

<img src="../../思维导图/JS继承.png">

## 1. 原型链实现继承

### 1.1 代码实现

```javascript
// 父类型
function SuperType() {
  this.property = true;
}
SuperType.prototype.getSuperValue = function () {
  return this.property;
};

// 子类型
function SubType() {
  this.subproperty = false;
}

// 子类型继承 SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function () {
  return this.subproperty;
};

var instance = new SubType();
console.log(instance.getSuperValue()); // true
```

> 原型链实现继承的关键代码：SubType.prototype = new SuperType();

- 本质上是重写了子类型的原型对象，其值为父类型的实例对象。这样的话，就可以通过原型链查找到父类型的属性和方法

### 1.2 存在的问题

- 第一个问题：若父类型中存在包含引用类型的实例属性，则该父类型的引用类型的实例属性会被所有的子类型共享

```javascript
// 父类型
function SuperType() {
  this.colors = ['red', 'blue', 'green'];
}

// 子类型
function SubType() {}

// 通过原型链实现继承
SubType.prototype = new SuperType(); // { colors: ['red', 'blue', 'green'] }

// 实例 1
var instance1 = new SubType();
// 操作实例 1 的 colors 属性
instance1.colors.push('black');
console.log(instance1.colors); // ['red', 'blue', 'green', 'black']

// 实例 2
var instance2 = new SubType();
console.log(instance2.colors); // ['red', 'blue', 'green', 'black']
```

- 第二个问题：在创建子类型实例时，不能向父类型的构造函数中传递参数

## 2. 借用构造函数

### 2.1 代码实现

```javascript
// 父类型
function SuperType(name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}

// 子类型
function SubType() {
  // 执行父类型构造函数，且修改其运行时的 this 值
  // this 值表示子类型的实例对象
  // 所以当 new SubType 时，此时子类型的实例对象就拥有了 colors 属性
  SuperType.call(this, 'Bob');
}

var instance1 = new SubType();
instance1.colors.push('black');
console.log(instance1.colors); // ['red', 'blue', 'green', 'black']

var instance2 = new SubType();
console.log(instance2.colors); // ['red', 'blue', 'green']
```

> 借用构造函数实现继承关键代码：SuperType.call(this)

- 解决了 `原型链实现继承` 中的子类型中的引用类型的实例属性会被共享以及不能给父类型构造函数传参的问题

### 2.2 存在的问题

- 因为所有的方法都要在构造函数里定义，所以无法共享方法

## 3. 组合继承

组合继承就是将 `原型链实现继承` 和 `借用构造函数实现继承` 结合起来

### 3.1 代码实现

```javascript
// 父类型
function SuperType(name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function () {
  console.log(this.name);
};

// 子类型
function SubType(name, age) {
  // 继承属性
  SuperType.call(this, name);
}
// 继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
```

> 组合继承避免了原型链和借用构造函数的缺陷，融合了它们的优点，成为了 js 中最常用的继承模式

- 使用 `借用构造函数` 继承属性
- 使用 `原型链` 继承方法

### 3.2 存在的问题

- 会调用两次父类型的构造函数

## 4. 原型式继承

### 4.1 代码实现

```javascript
function object(o) {
  // 定义 F 构造函数
  function F() {}
  // 将需要继承的对象赋值给 F 的原型
  F.prototype = o;
  // 返回实例对象，通过该实例对象可以访问到 F 的原型对象，所以可以访问到 o 对象的属性
  return new F();
}

// 或者通过 Object.create() 方法实现
```

## 5. 寄生式继承

### 5.1 代码实现

```javascript
function createAnother(original) {
  // 使用原型式继承的 object 方法（如上）
  var clone = object(original);
  // 添加方法，对 clone 对象增强
  clone.sayHi = function() {
    console.log('hi);
  }
  return clone;
}
```

## 6. 寄生组合式继承

### 6.1 代码实现

```javascript
function inheritPrototype(subType, superType) {
  // 复制一份父类型的原型对象
  var prototype = Object(superType.prototype);
  // 然后赋值给子类型的原型
  // 这样子类型的实例就可以通过原型访问到父类型原型上的方法和属性了
  prototype.constructor = subType;
  subType.prototype = prototype;
}

// 父类型
function SuperType(name) {
  this.name = name;
  this.colors = ['red', 'blue', 'green'];
}
SuperType.prototype.sayName = function () {
  console.log(this.name);
};

// 子类型
function SubType(name, age) {
  // 继承属性
  SuperType.call(this, name);
  this.age = age;
}
// 继承方法
inheritPrototype(SubType, SuperType);
```

> 寄生组合式继承结合了寄生和组合的优点，是引用类型最理想的继承范式

- 复制父类型的原型对象，赋值给子类型
