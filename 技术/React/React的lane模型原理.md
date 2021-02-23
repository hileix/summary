# React 的 lane 模型原理

lane 模型是一套表示 `优先级的机制`，它有以下特点：

- 可以表示 `优先级` 的不同。
- 可能同时存在几个同优先级的更新，所以还需要能够表示 `批` 的概念。
- 可以很方便的进行 `优先级` 相关的计算。

## 1. lane 模型和赛车赛道

lane 模型就和赛车赛道一样。lane 模型采用了 31 位的二进制数表示。

```js
const TotalLanes = 31;

export const NoLanes: Lanes = /*                        */ 0b0000000000000000000000000000000;
export const NoLane: Lane = /*                          */ 0b0000000000000000000000000000000;

export const SyncLane: Lane = /*                        */ 0b0000000000000000000000000000001;
export const SyncBatchedLane: Lane = /*                 */ 0b0000000000000000000000000000010;

export const InputDiscreteHydrationLane: Lane = /*      */ 0b0000000000000000000000000000100;
const InputDiscreteLanes: Lanes = /*                    */ 0b0000000000000000000000000011000;

const InputContinuousHydrationLane: Lane = /*           */ 0b0000000000000000000000000100000;
const InputContinuousLanes: Lanes = /*                  */ 0b0000000000000000000000011000000;

export const DefaultHydrationLane: Lane = /*            */ 0b0000000000000000000000100000000;
export const DefaultLanes: Lanes = /*                   */ 0b0000000000000000000111000000000;

const TransitionHydrationLane: Lane = /*                */ 0b0000000000000000001000000000000;
const TransitionLanes: Lanes = /*                       */ 0b0000000001111111110000000000000;

const RetryLanes: Lanes = /*                            */ 0b0000011110000000000000000000000;

export const SomeRetryLane: Lanes = /*                  */ 0b0000010000000000000000000000000;

export const SelectiveHydrationLane: Lane = /*          */ 0b0000100000000000000000000000000;

const NonIdleLanes = /*                                 */ 0b0000111111111111111111111111111;

export const IdleHydrationLane: Lane = /*               */ 0b0001000000000000000000000000000;
const IdleLanes: Lanes = /*                             */ 0b0110000000000000000000000000000;

export const OffscreenLane: Lane = /*                   */ 0b1000000000000000000000000000000;
```

- 从上到下，不同的 lane 占领了不同的 “赛道”，也就可以表示不同的优先级。
- 在其中有 lanes，占领了多个赛道，所以就可以 `批` 的概念。
- 由于采用了 31 位的二进制数，可以通过 [位运算](../前端开发/位运算.md) 很方便的进行优先级相关的运算。
