# Redux vs. Vuex

标签（空格分隔）： redux vuex

---

## 前言

了解这两个之前，首先要了解 **Flux** 思想，**Flux** 是一种前端状态管理架构思想，包括三个核心概念： **Views**, **Stores** 和 **Dispatcher**，还有一些基本概念： **Action**，**Action Types**，**Action Creators**等。

### 核心概念

- **Views** 就是视图。负责渲染界面，捕获用户事件，从**Stores**获取数据。
- **Stores** 用来管理数据。一个 Store管理一个区域的数据，当数据变化时会通知Views。
- **Dispatcher** 接收新数据然后传递给 Stores，Stores 更新数据并通知 Views。

### Flux 单向数据流

Flux 模式的核心在于 **单向数据流**，结合上面的概念理解 Flux 数据流的顺序是：

> View 发起 Action -> Dispatcher 收到 Action -> Dispatcher 通知 Stores 进行相应的更新 -> Stores 状态更新之后通知 Views 进行改变。

这个数据流如下图所示：

![](http://ww1.sinaimg.cn/large/005JoIL8gy1fi0lwfkhr3j30jg05wq36.jpg)

## Redux and Vuex

Redux 和 Vuex 都是基于 Flux 架构的产品，Redux是一个针对于 Flux 进行改进之后的一个状态管理器，而 Vuex 也是，也借鉴了 Redux 的优点，但是 Vuex 是只使用与 Vue 的一个状态管理器。

## Redux

Redux 官方的描述 `Redux is a predictable state container for JavaScript apps.`，其中 `predictable` 和 `state container` 体现了它的作用。

1. 这里需要先知道 redux 作为一个 `state container（状态容器）` 到底是啥？ Redux 其实是通过 state 来实现应用的状态管理，就需要一个专门管理 State 的地方（容器），就是 Store。

2. 对于 `predictable` ，这涉及到函数式编程的思想，Redux 使用 reducer 纯函数来管理 state 的更新，相同的输入一定对应着相同的输出， 输入确定的 state，reducer 函数就一定会输出确定的 state，因此就是实现了 state 的可预测（这里需要说明一点，Redux 的 state 是只读的，不可改变的，需要通过 Reducer 来产生 newState 实现 state 的更新）。

相对于 Flux 的核心概念，Redux 的核心概念应该是： `Actions`、`Reducers` 和 `Store`, 另外 Redux 还有一个亮点就是 `Middleware`。

### 核心概念

- **Actions** 是动作行为的抽象，是向 Store 传递数据信息的一个动作行为对象（一般是用户触发的交互信息），是改变 state 的唯一方法。
- **Reducers** 其实是一个函数： `(state, action) => newState`，是根据 action 来更新 state 的计算过程。
- **Store** 是保存数据的地方，其实就是状态容器。而且整个应用只有一个 Store。
- **Middleware** 其实是高阶函数，当 Store.dispatch 发起一个 action 的时候，可以用中间件作用于 dispatch 返回一个新的 dispatch（附加了该中间件功能）。

### Redux 相对于 Flux 的改进：

- 引入 State。通过 State 来实现状态管理， 专门管理 State 的地方就是 Store（并且在 Redux，Store是唯一的，即应用中所有 State 形成的一个状态树）
- 引入 reducer。通过 Reducer 中返回新的对象（newState）来作为应用的新状态。（Redux 使用 `(state, action) => newState` 来作为其核心思想，而来践行这个公式的就是 reducer 函数）
- 将 Dispatcher 引入 到 Store 中。

### Redux 数据流

> View 调用了 store.dispatch 发起 Action -> store接受Action（action 传入 Reducer 函数 -> Reducer 根据 `(state, action) => newState` 产生新的 state） ->  通知 store.subscribe 订阅的重新渲染

## Vuex

Vuex 是专门为 Vue.js 设计的状态管理库，以利用 Vue.js 的细粒度数据响应机制来进行高效的状态更新。同样基于Flux架构，同时也借鉴了 Redux 的优点。


### Vuex 核心概念

- **State** 理解为保存数据变量的状态
- **Getters** 可理解为 store 的计算属性，传入 state，得到 state 的一些衍生状态（对应 vue 的 computed）
- **Mutations** 类似于 Redux 的 reducer，提交一个 mutation 就会响应改变 state，从而触发视图更新
- **Actions** 类似于 mutation，只是 action 是间接提交一个 mutation 的方式（因为 mutation 是同步执行的，对于异步执行的就要通过 action 进行操作之后，提交一个 mutation）
- **Modules** 主要是对太复杂的数据进行分而治之，划分模块来管理，每个模块拥有各自的 state, getter, mutation, action。

### Vuex 数据流

View 调用 store.commit 提交对应的 mutation 函数（或者触发 action 来间接提交 mutation） -> mutation 函数进行相应的 state 的变更 -> store 改变(vue检测到数据变化自动渲染)

## Redux vs. Vuex

首先 Vuex 借鉴了 Redux 的有点，所以有挺多共同点的：

1. 两者都是基于 Flux 的实现，具备 Flux 的一些特征，都是单项数据流动。
2. 整个应用维持单一的 state tree， 也就是用一个 store 来进行数据的管理。
3. 都有唯一的 update state 机制，Redux 是通过 reducer 产生新的 state，Vuex 是通过 mutation 进行 state 的修改。
4. 都可以使用扩展，Redux 通过 middleware 中间件的形式，Vuex 通过 plugins 的形式。

不同之处：

1. 都是状态管理器，但是 Redux 是 JS 的状态管理， 不局限于单一的界面库；而 Vuex 是 vue 的状态管理，只用于vue。
2. 更新 state 的方式不同。Redux 中的 state 是不可改变的（redux 提倡 immutable），每次 action 所触发的 reducer 函数只会返回一个新的 state，来更新 store；Vuex 则不同，由于 Vue 的数据绑定机制， 所以 Vuex 是通过在 mutation 函数中直接对 state 进行修改， 从而更新 store。
3. 更新 store 的机制也不同。Redux 是通过 Store.subscribe(listener) 来对 state 的监听，当 state 更新就会触发 响应视图的更新； Vuex 则是因为其数据绑定机制，所以只需要改变数据，就会触发对应视图的更细。

## 再说两句

这篇总结，主要是针对自己使用了两者之后的一些体会心得，只谈理论以及两者的比较，不谈使用教程。

关于两者的使用，我觉得 Redux 和 Vuex 的文档都写得太棒啦！关于如何使用，文档写得很详细，而且把他们的精华亮点也都在文档里写出来了。









