# React 的生命周期

https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/

<img src="../../思维导图/React的生命周期.png">

还有一些生命周期在上面没有提到（在 React16 中被标记为不安全的生命周期，不推荐使用）：

- componentWillMount

在 constructor 后执行。可以用来获取数据，但不推荐。

- componentWillReceiveProps

在 props 发生改变时，会立即执行。一般用来通过 props 改变本组件的 state。

- componentWillUpdate

在 render 之前执行。

为什么上面三个生命周期不推荐使用？

查看[此博客](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)
