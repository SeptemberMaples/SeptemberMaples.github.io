---
title: React VS Vue —— 你需要知道的前端两大“框架”的异同
date: 2019-07-29 10:48:31
tags:
 - js
 - 前端
categories:
  - 前端
---

React 和 Vue 这两个极其优秀的前端“框架”基本上占据了前端开发的半壁江山甚至更多。作为前端开发者，你可以首选 React 或者 Vue 其中一个，但是如若尝试将两者进行对比，那么一定会收获很多。相信在面试中，两者的差异区别也是提到频率相当高的。

这里，我们从以下五个方面进行比较：
- 数据绑定
- 组件化和数据流
- 数据状态管理
- 渲染和更新
- 社区

<!-- more -->

### 数据绑定

Vue 在数据绑定上，采取了**双向绑定**策略，依靠 Object.defineProperty （Vue 3.0 已迁移到 Proxy）以及监听 DOM 事件实现。具体实现方法简单来说数据改变，依赖对数据进行拦截 / 代理；视图改变，依赖 DOM 事件（如 onInput、onChange 等）。Vue 实例中的 data 和 模版展现是一条线，无论谁被修改，另一方也会发生变动。

> Vue实现数据双向绑定的原理：Object.defineProperty（）
vue实现数据双向绑定主要是：采用**数据劫持结合发布者-订阅者模式**的方式，通过`Object.defineProperty()`来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应监听回调。当把一个普通 Javascript 对象传给 Vue 实例来作为它的 data 选项时，Vue 将遍历它的属性，用 Object.defineProperty 将它们转为 getter/setter。用户看不到 getter/setter，但是在内部它们让 Vue 追踪依赖，在属性被访问和修改时通知变化。

> vue的数据双向绑定 将MVVM作为数据绑定的入口，整合Observer，Compile和Watcher三者，通过Observer来监听自己的model的数据变化，通过Compile来解析编译模板指令，最终利用watcher搭起observer和Compile之间的通信桥梁，达到数据变化 —>视图更新；视图交互变化（input）—>数据model变更双向绑定效果。

值得一提的是 Vue 也支持计算属性，即 computed 属性，这个策略和 React-redux 当中的 mapStateToProps 有异曲同工之妙，都是通过计算，将所需要的数据注入给组件使用。

需要区分清楚的是：双向绑定和单向数据流并没有直接关联，双向绑定是指数据和视图之间的绑定关系，而单向数据流是指组件之间数据的传递。

React 并没有数据和视图之间的双向绑定，它的策略是“局部刷新”。当数据发生变化时，直接重新渲染组件，以得到最新的视图。这种“无脑”刷新的做法看似粗暴，但是换来的简单直观，并且 React 本身在性能上也提供了一定保障。

### 组件化和数据流

Vue 中组件不像 React 组件，它不是完全以组件功能和 UI 为维度划分的，而 Vue 组件本质是一个 Vue 实例。每个 Vue 实例在创建时都需要经过：设置数据监听、编译模版、应用模版到 DOM，在更新时根据数据变化更新 DOM 的过程。在这个过程中，类似 React 也提供了生命周期方法。

Vue 组件间通信或者说组件间数据流如同 React，也是单向的。数据流向也很类似：props 实现父组件向下传递数据，events 实现子组件向上发送消息给父组件，React 中是基于 props 的回调实现子组件向父组件传递数据（Vue 也支持）。

当然，这两种框架也分别通过 context 和 provide/inject 实现了跨层级数据通信，它们的实现也是非常类似的。

### 数据状态管理

对于较为复杂的数据状态，Redux 是 React 应用最常用的解决方案。这里需要说明的是：Redux 和视图无关，它只是提供了数据管理的流程，因此 Vue 使用 Redux 也是完全没有问题的。

当然，Vue 中更常用的是 Vuex，其借鉴了 Redux（Flux），也具有和 Redux 相同的 store 概念，组件不允许直接修改 store state，而是需要 dispatch action 来通知 store 的变化。但是这个过程不同于 Redux 的函数式思想，Vuex 改变 store 的方法支持提交一个 mutation。mutation 类似于事件发布订阅系统：每个 mutation 都有一个字符串来表示事件类型（type）和一个回调函数（handler）以进行对应的修改。

另一个显著区别是：*在 Vuex 中，store 是被直接注入到组件实例中的，因此用起来更加方便。而 Redux 需要 connect 方法，把 props 和 dispatch 注入给组件。*

**造成这些不同的本质原因是：**

- Redux 提倡不可变性，而 Vuex 的数据是可变的，Redux 中 reducer 每次都会生成新的 state 以替代旧的 state，而 Vuex 是直接修改；
- Redux 在检测数据变化的时候，是通过浅比较的方式比较差异的，而 Vuex 其实和 Vue 的原理一样，是通过遍历数据的 getter / setter 来比较。

### 渲染和更新

就像上面所提到的，React 和 Redux 倡导不可变性，更新需要维持不可变原则；而 Vue 对数据进行了拦截/代理，因此它不要求不可变性，而允许开发者修改数据，以引起响应式更新。

React 更像 MVC 或者 MVVM 模式中的 view 层，但是搭配 Redux 等，它也是一个完整的 MVVM 类库。Vue 直接是一个典型 MVVM 模式的体现，虽然它一直标榜自己也只是 View 层，但是毫无疑问它本身包含了对数据的操作。比如，Vue 文档中经常会使用 VM（ViewModel 简称），这个变量名表示 Vue 实例，其命名让人想到 MVVM，这是 MVVM 模式的体现。

React 所有组件的渲染都依靠灵活而强大的 JSX。**JSX 并不是一种模版语言，而是 JavaScript 表达式和函数调用的语法糖。在编译之后，JSX 被转化为普通的 JavaScript 对象，用来表示虚拟 DOM。**

Vue templates 是典型的模版，这相比于 JSX，表达更加自然。在底层实现上，
**Vue 模版被编译成 DOM 渲染函数，结合响应系统，进行数据依赖的收集**。Vue 渲染的过程如下：

* new Vue，进行实例化
* 挂载 $mount 方法，通过自定义 Render 方法、template、el 等生成 Render 函数，准备渲染内容
* 通过 Watcher 进行依赖收集
* 当数据发生变化时，Render 函数执行生成 VNode 对象
* 通过 patch 方法，对比新旧 VNode 对象，通过 DOM Diff 算法，添加、修改、删除真正的 DOM 元素

当然 Vue 也可以支持 JSX。

关于更新性能问题，简单来说，在 React 应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树。当然我们可以使用 PureComponent，或是手动实现 shouldComponentUpdate 方法，来规避不必要的渲染。但是这个实现过程要知悉数据状态结构，也需要一定的额外负担。

在 Vue 应用中，组件的依赖是在渲染过程中自动追踪的，因此系统能精确知晓哪个组件需要被重渲染。从理论上看，**Vue 的渲染更新机制更加细粒度，也更加精确**。

### 社区

这两个框架都具有非常强大的社区，但是对于社区的理念，Vue 和 React 稍有不同。举个例子：路由系统的实现。

Vue 的路由库和状态管理库都是由官方维护的，并且与核心库是同步更新的。而 React 把这件事情交给了社区，比如 React 应用中，需要引入 react-router 库来实现路由系统。


---

参考：LucasHC（侯策）- 《前端开发核心知识进阶》