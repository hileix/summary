# React 设计理念

React 的设计理念是 `快速响应`。那如何做到 web 的快速响应呢？这就得找到 web 快速响应的瓶颈。

## 1. web 快速响应的瓶颈

- CPU 瓶颈

CPU 瓶颈指执行 JS 代码的瓶颈。在 web 中，GUI 渲染线程和 JS 引擎是互斥的，当 JS 代码执行耗费了大量的时间时，GUI 渲染线程就会被冻结，页面就看起来卡。

- IO 瓶颈

IO 瓶颈指进行 HTTP 网络请求数据的瓶颈。在 web 中，请求数据是异步的，从服务端请求得到数据是需要时间的。长时间的加载状态，会导致用户体验不佳。

## 2. React 如何解决 CPU 瓶颈？

通过时间分片来解决。

在每一帧中会分配 5ms 的时间让 JS 引擎去执行 JS 代码。执行的过程可以被 React 中断和恢复。

## 3. React 如何解决 IO 瓶颈？

我们知道请求数据的时间长短在前端是改变不了的。那前端做哪些工作可以减轻用户对网络延迟的感知呢？

[通过将人机交互研究的结果整合到真实的 UI 中](https://zh-hans.reactjs.org/docs/concurrent-mode-intro.html#putting-research-into-production)来解决。

例如，研究表明，在屏幕之间切换时显示过多的中间加载状态会使切换的速度变慢。在 React 中可以通过 `Suspense/useDeferredValue` 来解决用户对网络延迟的感知问题。
