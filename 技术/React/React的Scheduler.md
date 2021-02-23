# React 的 Scheduler

- 时间切片
- 优先级调度

## 1. 时间切片

```
一个task(宏任务) -- 队列中全部job(微任务) -- requestAnimationFrame -- 浏览器重排/重绘 -- requestIdleCallback
```

在一帧的时间内，浏览器会做以上的工作。React 给每一帧分配 5ms 的时间来运行 JS 任务。那是在什么时候运行 JS 任务呢？

React 在 MessageChannel 的回调中执行 JS 任务。如果当前环境不支持 MessageChannel 的话，就会使用 setTimeout 来代替。

## 2. 优先级调度

在 React 中，不同的任务有着不同的优先级。

```js
// 有以下优先级
ImmediatePriority:立即执行的优先级，优先级最高
UserBlockingPriority:用户操作的优先级
NormalPriority:正常的优先级
LowPriority:低优先级
IdlePriority:空闲优先级
```

不同的优先级意味着什么？意味着他们有不同的任务过期时间。

```js
// Times out immediately
var IMMEDIATE_PRIORITY_TIMEOUT = -1;
// Eventually times out
var USER_BLOCKING_PRIORITY_TIMEOUT = 250;
var NORMAL_PRIORITY_TIMEOUT = 5000;
var LOW_PRIORITY_TIMEOUT = 10000;
// Never times out
var IDLE_PRIORITY_TIMEOUT = maxSigned31BitInt;
```

如果一个任务的的过期时间比当前时间还短，表示他已经过期了，需要立即被执行。

### 2.1 小顶堆实现优先级队列

在 React 项目中，会存在很多不同优先级的任务，他们对应着不同的过期时间。这些任务被分为两类：

- 未过期任务
- 已过期任务

所以在 Scheduler 中存在两个队列：

- timerQueue：未过期任务队列
- taskQueue：已过期任务队列

React 会频繁的操作这两个队列，所以为了能在 O(1)复杂度找到两个队列中时间最早的那个任务，Scheduler 采用了小顶堆数据结构实现了优先级队列。

两个队列会进行如下的操作：

1. 当有未过期的任务被注册时，就会将其插入到 timerQueue 中并根据过期时间重新排列 timerQueue 中任务的顺序。
2. 当 timerQueue 中有任务过期的任务，就会将其取出并插入到 taskQueue 中。
3. 取出 taskQueue 中最早过期的任务，并执行它。
