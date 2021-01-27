# JS 常见函数的实现

## 实现 new 运算符

new 运算符会为构造函数创建一个实例对象。

例如：

```js
// 定义 Person 对象
function Person(name, age) {
  this.name = name;
  this.age = age;
}

// new 一个 Person 对象的实例
// p1 = { name: 'xl', age: 18 }
const p1 = new Person('xl', 18);
```

new 关键字会进行如下的操作：

1. 创建一个空的 js 对象（即{}）。
2. 将该对象的 `__proto__（隐式原型）` 指向构造函数的 `prototype（显式原型）` 对象。
3. 调用构造函数，且构造函数的 this 指向第一步创建的对象。
4. 如果调用的构造函数返回对象，则返回该对象；否则返回第一步创建的对象。

按照上面的操作，我们来实现以下 new 操作符：

```js
// Con:构造函数
// ...args:构造函数接受的参数
function _new(Con, ...args) {
  // 1. 创建一个空的 js 对象
  const obj = {};

  // 2. 将该对象的 `__proto__（隐式原型）` 指向构造函数的 `prototype（显式原型）` 对象。
  obj.__proto__ = Con.prototype;

  // 3. 调用构造函数，且构造函数的 this 指向第一步创建的对象。
  const res = Con.apply(obj, args);

  // 4. 如果调用的构造函数返回对象，则返回该对象；否则返回第一步创建的对象。
  return typeof res === 'object' ? res : obj;
}
```

## 实现 call

在函数上执行 call 会改变函数内部的 this 指向，并且执行该函数。call 也接收一个参数列表，用于向执行的函数中传递参数

例如：

```js
const foo = {
  value: 1,
};
function bar() {
  console.log(this.value);
}
bar.call(foo); // 1
```

调用 call 会执行以下操作：

1. 修改 bar 的 this 指向
2. 调用 bar 函数

call 的实现：

```js
// call 的实现
Function.prototype._call = function (ctx, ...args) {
  // ctx 为 null 时，this 指向 window
  const context = ctx || window;

  // this 表示调用 call 的函数
  context.fn = this;

  // 传参执行
  const result = context.fn(...args);

  // 删除上下文对象中的 fn
  delete context.fn;

  // 返回函数执行的结果
  return result;
};
```

call 实现的关键点在于对于 this 的理解和运用，上面的 `context.fn = this;` 可以用以下例子理解：

```js
const obj = {
  value: 1,
  fn: function () {
    // fn 在 obj 对象下，this 指向该对象
    console.log(this.value);
  },
};
```

## 实现 apply

apply 和 call 功能差不多，只不过在传递第二个参数时，`call 接收的是一个参数列表`，而 `apply 接收的是一个包含多个参数的数组`。

```js
// apply 的实现
Function.prototype._apply = function (ctx, args) {
  // ctx 为 null 时，this 指向 window
  const context = ctx || window;

  // this 表示调用 call 的函数
  context.fn = this;

  // 传参执行
  const result = context.fn(...args);

  // 删除上下文对象中的 fn
  delete context.fn;

  // 返回函数执行的结果
  return result;
};
```

## bind 的实现

bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的参数将会在传递的实参前传入作为它的参数。

例如：

```js
var foo = {
  value: 1,
};

function bar() {
  console.log(this.value);
}

// 返回一个函数
var bindFoo = bar.bind(foo);

bindFoo(); // 1
```

一个函数调用 bind() 方法时，会执行以下步骤：

1. 返回一个新函数
2. 新函数执行时，this 指向传入 bind() 的第一个参数，其他参数会作为实参传入新函数

```js
Function.prototype._bind = function (ctx, ...args) {
  // this 表示该函数，包含该函数
  const self = this;

  return function (params) {
    // 改变执行函数的 this，执行该函数，并且传入参数
    return self.apply(ctx, args.concat(params));
  };
};
```

但 bind 还有一个特点：当 bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效。

```js
// 改进版
Function.prototype._bind = function (ctx, ...args) {
  // this 表示该函数，包含该函数
  const self = this;

  return function f(params) {
    // 如果 this 是 f 函数的实例的话，表示是使用了 new 实例化对象
    if (this instanceof f) {
      return new self(args.concat(params));
    } else {
      // 改变执行函数的 this，执行该函数，并且传入参数
      return self.apply(ctx, args.concat(params));
    }
  };
};
```

## 实现防抖（debounce） 函数

防抖原理：在事件被触发 n 秒后再执行回调，如果在 n 秒内又被触发，则重新计时。

应用场景：

- input 搜索时，只有用户结束输入后的一段时间，才去请求
- 触发 resize/scroll 事件时

```js
function debounce(callback, time) {
  // 记录定时器 id
  let timer = null;

  // 返回一个需要执行的回调函数
  return function (...args) {
    // 每次执行前，都清除定时器
    clearTimeout(timer);

    // 记录 this 值
    const ctx = this;

    // 设定定时器
    timer = setTimeout(function () {
      // 到时间了，改变 callback 的 this 值，并且传入参数执行
      callback.apply(ctx, args);
    }, time);
  };
}
```

### 立即执行需求

希望立即执行函数，然后等到停止触发 n 秒后，才可以重新触发执行。

```js
function debounce(callback, time, immediate) {
  // 记录定时器 id
  let timer = null;

  // 是否立即执行
  let isFirstExec = immediate;

  // 返回一个需要执行的回调函数
  return function (...args) {
    // 每次执行前，都清除定时器
    clearTimeout(timer);

    // 记录 this 值
    const ctx = this;

    // 第一次执行
    if (isFirstExec) {
      isFirstExec = false;
      // 返回值
      return callback.apply(ctx, args);
    } else {
      // 设定定时器
      timer = setTimeout(function () {
        // 到时间了，改变 callback 的 this 值，并且传入参数执行
        callback.apply(ctx, args);
      }, time);
    }
  };
}
```

### 取消 debounce

希望可以取消 debounce 返回回调的执行。

```js
function debounce(callback, time, immediate) {
  // 记录定时器 id
  let timer = null;

  // 是否立即执行
  let isFirstExec = immediate;

  function debounce(...args) {
    // 每次执行前，都清除定时器
    clearTimeout(timer);

    // 记录 this 值
    const ctx = this;

    // 第一次执行
    if (isFirstExec) {
      isFirstExec = false;
      // 返回值
      return callback.apply(ctx, args);
    } else {
      // 设定定时器
      timer = setTimeout(function () {
        // 到时间了，改变 callback 的 this 值，并且传入参数执行
        callback.apply(ctx, args);
      }, time);
    }
  }

  // 添加一个取消的方法
  debounce.cancel = function () {
    clearTimeout(timer);
    isFirstExec = immediate;
  };

  return debounce;
}
```

## 实现节流（throttle）函数

节流（throttle）函数原理：持续触发事件，每隔一段时间，只执行一次事件。

应用场景：

- scroll 事件，每隔一秒计算一次位置信息
- input 框实时搜索并发送请求展示下拉列表，每隔一秒发送一次请求 (也可做防抖)

```js
// 利用时间戳实现
function throttle(callback, time) {
  let context, args;
  let previous = 0;

  return function (...args) {
    let now = +new Date();
    context = this;
    if (now - previous > time) {
      callback.apply(context, args);
      previous = now;
    }
  };
}

// 利用 setTimeout 实现
function throttle(callback, time) {
  let timer = null;
  return function (...args) {
    let ctx = this;

    if (!timer) {
      timer = setTimeout(function () {
        timer = null;
        callback.apply(ctx, args);
      }, time);
    }
  };
}
```

## 实现 JS 深拷贝

### 最简单的深拷贝

```js
function cloneDeep(obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

存在的问题：

1. 对象类型无法拷贝
2. 对存在循环引用的对象进行深克隆，会报错

```js

```
