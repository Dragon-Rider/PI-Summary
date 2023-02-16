- [1. 虚拟 DOM 是什么?](#1-虚拟-dom-是什么)
- [2. 虚拟 DOM 优劣如何?](#2-虚拟-dom-优劣如何)
- [3. react diff 原理, 如何从 O(n^3) 变成 O(n)](#3-react-diff-原理-如何从-on3-变成-on)
- [4. React如何进行组件/逻辑复用?](#4-react如何进行组件逻辑复用)
- [5. mixin、hoc、render props、react-hooks的优劣如何？](#5-mixinhocrender-propsreact-hooks的优劣如何)
- [6. react 是如何避免 xss 攻击的？](#6-react-是如何避免-xss-攻击的)
- [7. react hooks 里的 useEffect 如何只执行一遍](#7-react-hooks-里的-useeffect-如何只执行一遍)
- [8.  React 为什么不能用 index 做 key](#8--react-为什么不能用-index-做-key)
- [9.  React 为什么要在constructor 中 bind this？](#9--react-为什么要在constructor-中-bind-this)
- [10. 你是如何理解fiber的?](#10-你是如何理解fiber的)

## 1. 虚拟 DOM 是什么?
在 React 中, React 会先将代码转换成一个 JS 对象, 然后再将这个 JS 对象转换成真正的 DOM. 这个 JS 对象就是所谓的虚拟 DOM.

它可以让我们无须关注 DOM 操作, 只需要开心地编写数据,状态即可.

- 虚拟DOM本质上是`JavaScript对象`,是对`真实DOM的抽象`
```javascript
// 原生 html 对象
<div class="title">
      <span>Hello ConardLi</span>
      <ul>
        <li>苹果</li>
        <li>橘子</li>
      </ul>
</div>

// 转成 virtual dom 的结构
const VitrualDom = {
  type: 'div',
  props: { class: 'title' },
  children: [
    {
      type: 'span',
      children: 'Hello ConardLi'
    },
    {
      type: 'ul',
      children: [
        { type: 'li', children: '苹果' },
        { type: 'li', children: '橘子' }
      ]
    }
  ]
}
```
- 状态变更时，记录新树和旧树的差异
- 最后把差异更新到真正的dom中

## 2. 虚拟 DOM 优劣如何?
优点:
- **保证性能下限**: 虚拟DOM可以经过diff找出最小差异,然后批量进行patch,这种操作虽然比不上手动优化,但是比起粗暴的DOM操作性能要好很多,因此虚拟DOM可以保证性能下限
- **无需手动操作DOM**: 虚拟DOM的 diff 和 patch都是在一次更新中自动进行的,我们无需手动操作DOM,极大提高开发效率
- **跨平台**: 虚拟DOM本质上是JavaScript对象,而DOM与平台强相关,相比之下虚拟DOM可以进行更方便地跨平台操作,例如服务器渲染、移动端开发等等

缺点:
- 无法进行极致优化: 在一些性能要求极高的应用中虚拟DOM无法进行针对性的极致优化,比如VScode采用直接手动操作DOM的方式进行极端的性能优化

## 3. react diff 原理, 如何从 O(n^3) 变成 O(n)
1. 为什么是 O(n^3) ?
从一棵树转化为另外一棵树,直观的方式是用动态规划，通过这种记忆化搜索减少时间复杂度。由于树是一种递归的数据结构，因此最简单的树的比较算法是递归处理。确切地说，树的最小距离编辑算法的时间复杂度是 O(n^2m(1+logmn)), 我们假设 m 与 n 同阶， 就会变成 O(n^3)。

2. react diff 原理

简单的来讲, react 它只比较同一层, 一旦不一样, 就删除. 这样子每一个节点只会比较一次, 所以算法就变成了 O(n).

对于同一层的一组子节点. 他们有可能顺序发生变化, 但是内容没有变化. react 根据 key 值来进行区分, 一旦 key 值相同, 就直接返回之前的组件, 不重新创建.

这也是为什么渲染数组的时候, 没有加 key 值或者出现重复key值会出现一些奇奇怪怪的 bug .

除了 key , 还提供了选择性子树渲染。开发人员可以重写 shouldComponentUpdate 提高 diff 的性能。

  - 16.8 之前：同层比较，结合树的递归。
  - 16.8 之后：同层比较，通过 fiber 链表做比较。

## 4. React如何进行组件/逻辑复用?
抛开已经被官方弃用的 Mixin, 组件抽象的技术目前有三种比较主流:

- **高阶组件**（HOC）：传入一个组价，返回一个组件。是对组件能力的扩展。
  - 属性代理
  - 反向继承
- 渲染属性（render props）：props 是一个函数，这个函数的返回值又是一个组件。有点像 JS 里的 回调函数，这样写扩展性比较好，解决了代码复用的问题。
- react-hooks

## 5. mixin、hoc、render props、react-hooks的优劣如何？
**Mixin的缺陷**
- 组件与 Mixin 之间存在隐式依赖（Mixin 经常依赖组件的特定方法，但在定义组件时并不知道这种依赖关系）

- 多个 Mixin 之间可能产生冲突（比如定义了相同的state字段）

- Mixin 倾向于增加更多状态，这降低了应用的可预测性（The more state in your application, the harder it is to reason about it.），导致复杂度剧增

- 隐式依赖导致依赖关系不透明，维护成本和理解成本迅速攀升：
  - 难以快速理解组件行为，需要全盘了解所有依赖 Mixin 的扩展行为，及其之间的相互影响
  - 组价自身的方法和state字段不敢轻易删改，因为难以确定有没有 Mixin 依赖它
  - Mixin 也难以维护，因为 Mixin 逻辑最后会被打平合并到一起，很难搞清楚一个 Mixin 的输入输出
  ```javascript
  // 举例：https://mp.weixin.qq.com/s/W5pk2fuCHuRqPizwlnUnIQ
  const customMixin = {
    componentDidMount(){
      console.log( '------componentDidMount------' )
    },
    say(){
      console.log(this.state.name)
    }
  }

  const APP = React.createClass({
    mixins: [ customMixin ],
    getInitialState(){
      return {
        name:'alien'
      }
    },
    render(){
      const { name  } = this.state
      return <div> hello ,world , my name is { name } </div>
    }
  })
  ```

**HOC相比Mixin的优势**
- HOC通过外层组件通过 Props 影响内层组件的状态，而不是直接改变其 State不存在冲突和互相干扰,这就降低了耦合度

- 不同于 Mixin 的打平+合并，HOC 具有天然的层级结构（组件树结构），这又降低了复杂度

**HOC的缺陷**
- 扩展性限制: HOC 无法从外部访问子组件的 State因此无法通过shouldComponentUpdate滤掉不必要的更新,React 在支持 ES6 Class 之后提供了React.PureComponent来解决这个问题

- Ref 传递问题: Ref 被隔断,后来的React.forwardRef 来解决这个问题

- Wrapper Hell: HOC可能出现多层包裹组件的情况,多层抽象同样增加了复杂度和理解成本

- 命名冲突: 如果高阶组件多次嵌套,没有使用命名空间的话会产生冲突,然后覆盖老属性

- 不可见性: HOC相当于在原有组件外层再包装一个组件,你压根不知道外层的包装是啥,对于你是黑盒

**[Render Props](https://react.docschina.org/docs/render-props.html) 优点**
- 上述HOC的缺点Render Props都可以解决

**Render Props缺陷**
- 使用繁琐: HOC使用只需要借助装饰器语法通常一行代码就可以进行复用,Render Props无法做到如此简单
- 嵌套过深: Render Props虽然摆脱了组件多层嵌套的问题,但是转化为了函数回调的嵌套

**React Hooks优点**
- 简洁: React Hooks解决了HOC和Render Props的嵌套问题,更加简洁

- 解耦: React Hooks可以更方便地把 UI 和状态分离,做到更彻底的解耦

- 组合: Hooks 中可以引用另外的 Hooks形成新的Hooks,组合变化万千

- 函数友好: React Hooks为函数组件而生,从而解决了类组件的几大问题:
  - this 指向容易错误
  - 分割在不同声明周期中的逻辑使得代码难以理解和维护
  - 代码复用成本高（高阶组件容易使代码量剧增）


**React Hooks缺陷**
- 额外的学习成本（Functional Component 与 Class Component 之间的困惑）

- 写法上有限制（不能出现在条件、循环中），并且写法限制增加了重构成本

- 破坏了PureComponent、React.memo浅比较的性能优化效果（为了取最新的props和state，每次render()都要重新创建事件处函数）

- 在闭包场景可能会引用到旧的state、props值

- 内部实现上不直观（依赖一份可变的全局状态，不再那么“纯”）

- React.memo并不能完全替代shouldComponentUpdate（因为拿不到 state change，只针对 props change）

关于react-hooks的评价来源于官方[react-hooks RFC](https://github.com/reactjs/rfcs/blob/main/text/0068-react-hooks.md#drawbacks)

## 6. react 是如何避免 xss 攻击的？
jsx 里的内容或者 `escaped`（转义），所有的内容在渲染之前都被转换成了字符串，关键字会被替换掉。（[link1](https://react.docschina.org/docs/introducing-jsx.html)，[link2](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-in-html)）

## 7. react hooks 里的 useEffect 如何只执行一遍
如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。

如果你传入了一个空数组（[]），effect 内部的 props 和 state 就会一直拥有其初始值。尽管传入 [] 作为第二个参数更接近大家更熟悉的 componentDidMount 和 componentWillUnmount 思维模式；

## 8.  React 为什么不能用 index 做 key
普通： key在diff时能起到标记的作用，比如往一个数组前面添加一个元素，react通过key能清晰知道它只用新增一个节点，而另外两个节点可以直接复用，从而极大优化性能。

进阶：react diff diff 比较是通过两个步骤：

1. 首先比较 key ，若key不同，再去历史 key map 里查（看看是不是移动了），如果没有，直接重新创建节点

2. 如果 key 相同比较元素的 type，若 type 不同直接创建新节点

3. 如果 key 和 type 都相同说明组件没有变化，**`只做更新而不会新增`**，基于旧的节点和新的组件 props（非dom props） 更新

由于以上的特质，导致了如果不传 key（即 key 均为 null） 或者 key 用 index 替代的话（从前往后始终是 0，1，2），前后 diff 的时候 key 始终相同。这个时候如果 type 又是一致的，只会克隆一个组件，然后更新组件的 props。但是组件渲染后实际的 Dom 里的 props 是不变的，比如 input.value 的值。

引申：用 index 做 key，不出问题还好，一单出问题就会很难查。

高阶：react 的 diff 是`同级比较`，比较是先比较 `key`，再比较 `type`，如果 都相同，则认为是 update，只会更新 props ，key 用 index 在新增节点的时候可能会有问题。

参考：[link1](https://juejin.cn/post/6844903527836286989)、[link2](https://juejin.cn/post/7121544519387971620#heading-6)

## 9.  React 为什么要在constructor 中 bind this？
答1：1. class 类 默认是严格模式，普通函数的this如果不指定就会指向undefined，要在constructor函数中绑定或将类方法写为箭头函数。所以跟react无关，是因为js函数工作原理。 2.  箭头函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。箭头函数定义时this已经指向了当前类，就不会出现普通函数this没有指向的问题。

答2：constructor bind 之后 new 的 Class 组件新实例都会使 this 指向当前的实例；否者的话，this 会指向的 undefined（在严格模式下，全局对象将无法使用默认绑定，因此this会绑定到undefined。在ES6的class中，也会绑定到undefined）。当使用new关键字时，this指向新对象，同时箭头函数中的this也被赋值为了新对象且永远不会更改指向。

答3：JSX 实际是 `React.createElement` 的语法糖。去取这个this.handleClick属性时候，this已经不是我们在书写的时候认为的绑定在View上了。this会默认绑定，但是又是在`ES6的class中`，所以this绑定了undefined。
```javascript
class View extends React.Component {
    constructor(props){
        super(props);
    }

    handleClick(e) {
        console.log(e);
    }

    render(){
        return React.createElement(
            "a",
            { onClick: this.handleClick},
            "click me"
        );
    }
}
```
[link1](https://juejin.cn/post/6844904150589767688)、[link2](https://segmentfault.com/a/1190000038167700)、[link3](https://juejin.cn/post/6844903589786157063)

## 10. 你是如何理解fiber的?
**简化版**
React Fiber 是一种基于浏览器的 **`单线程调度算法`**。

React 16.8之前 ，reconcilation 算法实际上是递归，想要中断递归是很困难的，React 16 开始使用了循环来代替之前的递归.

Fiber：**一种将 recocilation （递归 diff），拆分成无数个小任务的算法；它随时能够停止，恢复。停止恢复的时机取决于当前的一帧（16ms）内，还有没有足够的时间允许计算。**

**详细版**
- **Schedule(调度器)**：排序优先级，让优先级高的任务先进行reconcile。

- **Reconcile(协调器)**：vdom 树 转 fiber 链表；Reconcile 的过程中会做 diff，打上增删改的标记（effectTag），并把对应的 dom 创建好；

- **Commit(渲染器)**：将 Reconciler 中打好标签的节点渲染到视图上

