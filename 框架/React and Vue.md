# React and Vue

标签（空格分隔）： react vue

---

## React

开发视角

- All in JS。

原理视角

- 数据试图分离，数据规模大的项目开发便捷。
- 虚拟dom（对于交互多的页面效果好），diff渲染，性能高效。

其他视角

- 更大的生态，更活跃的社区

## Vue

- 简单的语法和项目配置（基本vue-cli包办）
- 更小
- 对比起 React，Vue 拥有专门属于 Vue 的配套路由工具，状态管理器，插件等，这些配套的库更具针对性

## 共同点

- 用虚拟DOM实现快速渲染
- 轻量级UI库
- 组件化，模块化
- 都有对应的服务端渲染
- 都有配套的路由工具，状态管理器，各种 middleware，plugin

## 不同点

- Vue 使用模板（数据绑定表达式采用的是和 Angular 相似的 mustache 语法，而指令（特殊的HTML属性）用来向模板添加功能）；React 不使用模板，但是借助 JSX 在 JS 中创建 DOM。
- React 的 state 是不可变（immutable）的（React中是通过比较当前state和前一个state来决定何时在DOM中进行重渲染以及渲染的内容，因此需要不可变的state）；Vue中的数据是可变（mutated）的，Vue 由 data 进行驱动，所以同样的操作看起来更加简洁。
- React的渲染系统可配置性更强，并包含如shallow rendering这样的特性，可结合React的测试工具一起使用，从而大为提高代码的可测试性及可维护性。
- React 的单向数据流，加上 Redux 这样的状态管理器，可以更好的应对大型应用的数据，对于数据的流向和修改更加可预测，更加适合于大型应用。