- [1. 虚拟 DOM 是什么?](#1-虚拟-dom-是什么)

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