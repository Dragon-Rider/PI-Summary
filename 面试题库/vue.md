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