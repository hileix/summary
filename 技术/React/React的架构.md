# React 的架构

## 1. React15 的架构

React15 的架构由两部分组成：

- Reconciler（协调器）

协调器主要是对虚拟 DOM 进行 diff 算法。

- Renderer（渲染器）

渲染器主要是将需要改变的 dom 渲染到页面中。

渲染器在不同平台是不同的：

- ReactDom 渲染器（浏览器，SSR）
- ReactNative 渲染器（渲染 App 原生组件）
- ReactTest 渲染器（渲染 JS 对象）
- ReactArt 渲染器（canvas SVG）

**在 React15 的架构中，协调器和渲染器交替工作，整个交替工作同步完成后，会更新 DOM。**

## 2. React16 架构

React16 采用了 Fiber 架构。

React16 架构由三部分组成：

- Scheduler（调度器）

调度任务的优先级，高优任务优先进入 Reconciler。

- Reconciler（协调器）

负责找出变化的组件。

- Renderer（渲染器）

负责将变化的组件渲染到页面上。

**当 Scheduler 将任务交给 Reconciler 后，Reconciler 会为变化的虚拟 DOM 打上代表增/删/更新的标记。最后将打好标记的虚拟 DOM 交给 Renderer 进行渲染。**
