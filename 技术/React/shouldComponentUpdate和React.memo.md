# shouldComponentUpdate 和 React.memo

两者都是 React 性能优化的手段。

但它们有以下区别：

- shouldComponentUpdate 是生命周期函数，在 class 组件内；React.memo 是高阶组件，用于包裹函数组件。

- shouldComponent 优化的的本质是直接跳过 render；而 React.memo 优化的本质是复用最近以下的渲染结果，还是会走 render。

使用 PureComponent 会复写 class 组件内的 shouldComponent 生命周期函数，会对 state 和 props 进行浅比较。

React.memo 接受一个 `前后 props 是否相等` 的比较函数，可以自定义比较的逻辑。
