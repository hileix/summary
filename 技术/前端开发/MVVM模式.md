# MVVM 模式

## 1. 什么是 MVVM

<img src="./assets/MVVMPattern.png">

MVVM = Model-View-ViewModel

- Model(模型):包含了数据和逻辑。
- View(视图):视图层，即用户看到的界面。
- ViewModel(视图模型):是一个绑定器，能和 View 层和 Model 层进行通信。

MVVM 通过 ViewModel 层让 Model 和 View 解耦。

## 2. Vue 是 MVVM 框架吗？

Vue 并不完全是一个 MVVM 框架（有些地方并没有完全按照 MVVM 模式去设计）。详情请查看此[回答](https://www.zhihu.com/question/327050991/answer/701449139)。

完全遵循 MVVM 模式的有 KnockoutJS。

## 3. React 是 MVVM 吗？

不是，React 只是一个 M(数据层) 到 V(视图层) 的映射，由 M 控制着 V 的渲染。最多算一个 MV 的架构。详情请查看此[回答](https://www.zhihu.com/question/310674885)。
