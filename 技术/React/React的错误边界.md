# React 的错误边界

在 React 中，如果不设置错误边界，就会让代码中未捕获的错误导致应用白屏。

在 React 中，可以通过以下两个方法捕获错误：

- getDerivedStateFromError:静态方法。用于更新 state 使得下一次渲染能够显示降级后的 UI。

- componentDidCatch:实例方法。在这里可以将错误信息进行上报。

```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级后的 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你同样可以将错误日志上报给服务器
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级后的 UI 并渲染
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```
