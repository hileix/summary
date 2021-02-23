# React 的 bachedUpdates

## 1. React15 的 bachedUpdates

React15 的 bachedUpdates 是通过 Transaction(事务) 实现的。

参考：

- https://zhuanlan.zhihu.com/p/28532725
- https://zhuanlan.zhihu.com/p/78516581

## 2. React16 的 bachedUpdates

未开启 concurrent 模式时，bachedUpdates 还是会受 setTimeout 这样方法的限制。但当开启了 concurrent 模式后，bachedUpdates 在 setTimeout 这样的方法内 setState 也会批量更新。

## 3. 对比 Vue 的 bachedUpdates

vue 的 bachedUpdates 是通过将任务推入到微任务队列中 `延迟` 执行的。在浏览器环境中 Vue 会优先尝试使用 MutationObserver API 或 Promise，如果两者都不可用，则 fallback 到 setTimeout。
