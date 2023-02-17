- [1. 基础知识](#1-基础知识)
  - [1.1 Vue 的生命周期](#11-vue-的生命周期)
  - [1.2 Vue 的路由分类](#12-vue-的路由分类)
  - [1.1 Vue 的父子组件通信方式](#11-vue-的父子组件通信方式)
  - [1.4 vue在渲染列表的时候，要用写 key，key 的作用是什么？](#14-vue在渲染列表的时候要用写-keykey-的作用是什么)
  - [1.5 vue组件间通信方式有哪些？](#15-vue组件间通信方式有哪些)
  - [1.6 组件的 data 为什么不是一个对象，而要写成函数形式？](#16-组件的-data-为什么不是一个对象而要写成函数形式)
  - [1.7 vue3.0有哪些新特性？考察对新技术的学习程度](#17-vue30有哪些新特性考察对新技术的学习程度)
- [2. 原理相关](#2-原理相关)
  - [2.1 vue中响应式数据原理是什么？](#21-vue中响应式数据原理是什么)
  - [2.2 什么是MVVM？](#22-什么是mvvm)
  - [2.3 谈谈React和Vue 的区别？](#23-谈谈react和vue-的区别)
  - [2.4 什么是虚拟 dom](#24-什么是虚拟-dom)
- [3. 全家桶](#3-全家桶)
  - [3.1 Vuex Mutation 的解释](#31-vuex-mutation-的解释)

## 1. 基础知识
### 1.1 Vue 的生命周期
### 1.2 Vue 的路由分类
### 1.1 Vue 的父子组件通信方式
### 1.4 vue在渲染列表的时候，要用写 key，key 的作用是什么？
- key的作用是为了在列表数据发生变化时更新组件，key是给每一个vue节点的唯一id, 可以依靠key, 更准确, 更快的拿到就节点中对应的 vnode 对象。([link](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/1))

- 更快体现在：如果没有key，那么就会采用遍历查找的方式去找到对应的旧节点。有key的话，会生成一个key => index 的map映射。有key是map映射，无key是遍历查找。相比而言,map映射的速度更快。

- 更准确体现在：key的作用就是更新组件时判断两个节点是否相同。相同就复用，不相同就删除旧的创建新的。如果无key， 在绝大部分有自己的状态的列表组件中，复用了组件，保留了之前的状态，会造成"就地复用"的副作用。

- 更好预测：比如你想使用 <transition-group> 给列表加过渡动画，或想在被渲染元素是 <input> 时保持聚焦。在这些情况下，为每一个项目添加一个唯一的键值 (比如 :key="todo.id") 将会让 Vue 知道如何使行为更容易预测。

官方说明：[List Rendering - key](https://v2.cn.vuejs.org/v2/guide/list.html#%E7%BB%B4%E6%8A%A4%E7%8A%B6%E6%80%81)

### 1.5 vue组件间通信方式有哪些？
- 父子组件间的通信：
  - props 和 event， 父组件向子组件传递数据是通过prop传递的，子组件传递数据给父组件是通过$emit触发事件来做到的
  - $parent 和 $children，$parent,$children 获取当前组件的父组件和当前组件的子组件
- 非父子组件如何通信？
  - provide 和 inject，父组件中通过provide来提供变量，然后在子组件中通过inject来注入变量
  - eventbus， envetBus 平级组件数据传递 这种情况下可以使用中央事件总线的方式
  - dispatch 和 brodcast

**引申问题1**：子组件为什么不可以修改父组件传递的Prop？
回答：Vue提倡单向数据流,即父级 props 的更新会流向子组件,但是反过来则不行。这是为了防止意外的改变父组件状态，使得应用的数据流变得难以理解。如果破坏了单向数据流，当应用复杂时，debug 的成本会非常高。

### 1.6 组件的 data 为什么不是一个对象，而要写成函数形式？
回答：vue 的组件都是可复用的，一个组件创建好后，可以在多个地方复用，而不管复用多少次，组件内的 data 都应该是相互隔离，互不影响的，所以组件每复用一次，data 就应该复用一次，每一处复用组件的 data 改变应该对其他复用组件的数据不影响。

为了实现这样的效果，data 就不能是单纯的对象，而是以一个函数返回值的形式，所以每个组件实例可以维护独立的数据拷贝，不会相互影响。

官方说明：组件的 data 必须是一个函数。

### 1.7 vue3.0有哪些新特性？考察对新技术的学习程度
- Composition API： 更清晰代码逻辑和的通用逻辑复用。

- VDom的性能优化：VDom的更新(计算diff)性能提升1.3-2倍，SSR的速度提升了2-3倍。主要优化点包括：静态标记标记与提升、事件缓存。

- 全面支持typescript: typescript在Vue3.0源码中占比90%以上，Vue3.0将对tsx、class component等有更好的支持.

- Tree Shaking: Vue3.0做到了按需引入，更好支持tree shaking，有时候并不需要Vue全部的功能，打包时可以将无用的代码移除；

- Fragment：Vue3.0支持模板添加多个根节点，无需用一个根节点包裹，意味着render函数也可以返回数组了

- 自定义Render API: 提供标准的render API， weex和mpvue无需fork代码来增加一些自定义功能；

## 2. 原理相关
### 2.1 vue中响应式数据原理是什么？
- 当你把一个普通的 JavaScript 对象传入 Vue 实例作为 data 选项，Vue 将遍历此对象所有的 property，并使用 [Object.defineProperty](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 把这些 property 全部转为 [getter/setter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Working_with_Objects#%E5%AE%9A%E4%B9%89_getters_%E4%B8%8E_setters)。Object.defineProperty 是 ES5 中一个无法 shim 的特性，这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因。（官方文档：[如何追踪变化](https://v2.cn.vuejs.org/v2/guide/reactivity.html)）

- 每个属性都拥有自己的dep属性，存放他所依赖的watcher，当属性变化后会发布消息给订阅者，触发相应监听回调通知自己对应的watcher去更新。

![Vue Responsive Theory](https://github.com/Dragon-Rider/eragon.github.io/raw/main/imgs/PI-Summary/vue_responsive_theory.jpg "Vue Responsive Theory")

**延伸问题1**： 数组的响应式是如何做到的？为什么不采用Object.definePropertype相同的方法来处理

回答：数组的长度是不可控的，如果通过Object.definePropertype方法对数组的每一项进行拦截，`性能会比较差`；所以vue2选择了对方法（push,shift,pop,splice,unshift,sort,reverse）进行重写。改变了监听方式，从原本的直接监听结果变化这种思路变换到监听会导致结果变化的数组方法上即可。

**引申问题2**：Vue3中是使用proxy来实现响应式数据的原因是什么？

- 性能问题：defineProperty只能劫持对象的属性，而Proxy是直接代理对象。由于defineProperty 只能对属性进行劫持，需要遍历对象的每个属性，如果属性值也是对象，则`需要深度遍历`。而 Proxy 直接代理对象，不需要遍历操作。同时也能解决vue2中数组的监听问题；

- 声明式的响应式：defineProperty对新增属性需要手动进行Observe。

由于 Object.defineProperty劫持的是对象的属性，所以`新增属性时，需要重新遍历对象`，对其新增属性再使用 Object.defineProperty 进行劫持。也正是因为这个原因，使用Vue2.x给 data 中的数组或对象新增属性时，需要使用 vm.$set 才能保证新增的属性也是响应式的。

- **可以顺带考察一下es6的Proxy 和Reflect使用**

### 2.2 什么是MVVM？
`视图模型双向绑定`，是Model-View-ViewModel的缩写。所谓的MVVM其实就是Model、View 和ViewModel，**Vue是典型的 MVVM 设计模式**：

- Model：模型层（`数据层`），主要用于保存一些数据

- View： `视图层`，主要用于将后端数据借助各种元素呈现给用户，同时也可提供用户操作的入口

- ViewModel：视图模型层：该层也是mvvm中的核心层，主要用于作为Model个View两个层的`数据连接层，负责两个层之间的数据传递`。该层主要包含两大功能点：

    - DOM监听（DOM Listener） 用于监听dom元素的一些事件，如果dom元素发生变化在需要的时候会改变对应的data

    - 数据绑定（Data bindings）用于将model的改变反应在view上及时呈现给用户

MVVM（即 Vue 2.0）的实现分为三步：`数据劫持（object.defineProperty）、模板编译和双向绑定(发布者，订阅者模式)`。

答2：`ViewModel`是View和Model层的`桥梁`，数据会绑定到viewModel层并自动将数据渲染到页面中，视图变化的时候会通知 ViewModel 层更新数据。以前是操作DOM结构更新视图，现在是`数据驱动视图`。

参考：[link1](https://juejin.cn/post/7101516105289564196)、[link2](https://juejin.cn/post/7016593221815910408#heading-85)

### 2.3 谈谈React和Vue 的区别？
相同：其实react和vue大体上是相同的，比如都使用`虚拟DOM`高效的更新视图，都提倡`组件化`，都实现了`数据驱动视图`，都使用`diff算法`，也都对diff算法进行了优化，都有router库实现url到组件的映射，都有状态管理等等

不同：

- 组件化差异：

    - React 推荐的是 JSX+inline css 的写法，就是把 html，js，css 都写入 js 中，倡导 all in js。

    - vue 推荐 template 的写法，html，js，css 在一个文件中但彼此隔离；

- diff算法差异：vue 因为是双链表结构所以可以采用`双端对比`。React 没有在 fiber 上设置反向链表，所以没法用双端对比。因此 ，同样情况下 Vue 可以减少移动节点次数，减少不必要的性能损耗，更加的优雅。

- 数据流差异：Vue 是`双向数据绑定`；React 是 `单向数据流`

![Vue Data Stream](https://github.com/Dragon-Rider/eragon.github.io/raw/main/imgs/PI-Summary/vue_data_stream.jpg "Vue Data Stream")

- HOC和mixin：vue 2.0 和 React 16.8 之前的，React 用 HOC ，Vue 用 mixin 实现逻辑复用；现在都用 hooks 了（或者 composition api）; React 不用 mixin 是因为 mixin 侵入性太强了；

- 组件通行

    - vue：父子组件用 事件/回调 和 props，倾向于用 `事件`，全局用 provide/inject

    - react：父子组件用 `Callback` 和 props，通过 context 进行跨层级的通信

![Vue Components Communication](https://github.com/Dragon-Rider/eragon.github.io/raw/main/imgs/PI-Summary/vue_components_communication.jpg "Vue Components Communication")

React适合构建大型应用，鲁棒性更好；Vue 适合构建短平快的项目，灵活性更好，更简单好上手；

参考：[link1](https://juejin.cn/post/7144648542472044558#heading-16)、[link2](https://juejin.cn/post/7116141318853623839#heading-21)、[link3](https://juejin.cn/post/6844903668446134286)

### 2.4 什么是虚拟 dom
虚拟 DOM（Virtual DOM）本质上是JS 和 DOM 之间的一个映射缓存，它在形态上表现为一个能够描述 DOM 结构及其属性信息的 JS 对象。它主要存储在内存中。主要来说：

- 虚拟dom是一个js对象，存储在内存之中。

- 虚拟dom能够描述真实dom（存在一个对应关系）

- 当数据变化的时候，生成新的DOM，对比新旧虚拟DOM的差异，将差异更新到真实DOM上

[link](https://juejin.cn/post/7144648542472044558)

## 3. 全家桶
### 3.1 Vuex Mutation 的解释