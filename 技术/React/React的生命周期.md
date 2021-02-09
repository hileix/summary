# React 的生命周期

https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/

<img src="../../思维导图/React的生命周期.png">

还有一些生命周期在上面没有提到（不推荐使用的）：

- componentWillMount

在 constructor 后执行。可以用来获取数据，但不推荐。

- componentWillReceiveProps

在 props 发生改变时，会立即执行。一般用来通过 props 改变本组件的 state。

- componentWillUpdate

在 render 之前执行。

## React 生命周期相关的一些问题

1. 为什么不推荐在 componentWillMount 里获取数据？

因为 componentWillMount 在将来的 React 版本中可能会调用多次。
