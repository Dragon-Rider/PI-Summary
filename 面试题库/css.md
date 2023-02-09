- [1. 盒模型介绍](#1-盒模型介绍)
- [2. CSS垂直居中的实现，高度为宽度的50%的方法实现](#2-css垂直居中的实现高度为宽度的50的方法实现)
- [3. 弹性布局](#3-弹性布局)
- [4. 解释一下 BFC 是什么，可以用来做什么](#4-解释一下-bfc-是什么可以用来做什么)
  - [4.1 如何触发 BFC？](#41-如何触发-bfc)
  - [4.2 BFC 的特性与应用](#42-bfc-的特性与应用)
- [5. px，rem，vw 区别](#5-pxremvw-区别)
- [6. 解释：BEM, scoped css, css modules 与 css-in-js](#6-解释bem-scoped-css-css-modules-与-css-in-js)
- [7. 页面上弹窗的“滚动穿透”如何解决](#7-页面上弹窗的滚动穿透如何解决)


## 1. 盒模型介绍
**标准盒模型**： 一个块的总宽度 = width+margin(左右)+padding(左右)+border(左右)
在标准的盒子模型中，`width指content部分的宽度`（蓝色部分）。box-sizing: content-box 是W3C盒子模型
![Standard Box Model](https://github.com/Dragon-Rider/eragon.github.io/raw/main/imgs/PI-Summary/css_standard_box_model.png "Standard Box Model")


**怪异盒模型**： 一个块的总宽度 = width+margin（左右）（既 width 已经包含了 padding 和 border 值）
在IE盒子模型中，`width表示content+padding+border这三个部分的宽度`。box-sizing: border-box 是IE盒子模型
![Alternate Box Model](https://github.com/Dragon-Rider/eragon.github.io/raw/main/imgs/PI-Summary/css_alternate_box_model.png "Alternate Box Model")

## 2. CSS垂直居中的实现，高度为宽度的50%的方法实现
```
方法1：
    .center {
        position: absolute;
        top: 50%;
        left: 50%;
        -ms-transform: translate(-50%,-50%);
        -moz-transform: translate(-50%,-50%);
        -o-transform: translate(-50%,-50%);
        transform: translate(-50%,-50%);
        width: 80%;
        height: 0;
        padding-bottom: 40%; // 主要知识点是padding-bottom的百分比值是相对包含块的宽度来算的
    }

方法2：水平居中
    .center {
        margin: 0 auto;
    }

方法3：
    .center {
        position: absolute;
        width: 宽度值;
        height: 高度值;
        left: 50%;
        top: 50%;
        margin-left: -(宽度值/2);
        margin-top: -(高度值/2);
    }

方法4：
.parent {
    display:flex;
    align-items: center;/*垂直居中*/
    justify-content: center;/*水平居中*/
    width:100%;
    height:100%;
    background-color:red;
}

方法5：
// 让明确宽高的盒子垂直水平居中
.child {
    width: 100px;
    height: 100px;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: #8ac6d1;
    margin: auto;
}
```
[link](https://www.cnblogs.com/echolun/p/11353627.html)

## 3. 弹性布局
问题1：类似美团外卖菜单页，左边菜品种类固定 50 px，右边菜单自适应，如何实现。(link)[https://codesandbox.io/s/bfcshi-xian-zi-gua-ying-uzos3]
```
问题：
.parent {
}

.child-left {
}

.child-right {
}

// 解答1 flex：
.parent {
    display:flex;
}

.child-left {
  flex: 0 0 50px;
}

.child-right {
  flex: 1 1 auto;
}

// 解答2 BFC 实现
.child-left {
  width: 50px;
  float: left;
}

.child-right {
  overflow: hidden;
  /*
  注意：这里不能设置成浮动，否者第二个元素会换行展示。因为浮动后，没设置宽度，块级元素默认有 100% 的宽度。
  float: left;
  */
}
```

问题2：如果没有 flex 要做响应式设计怎么实现呢？考察 media-query。

## 4. 解释一下 BFC 是什么，可以用来做什么
BFC 即 Block Formatting Contexts (块级格式化上下文)，它属于上述定位方案的普通流。

**具有 BFC 特性的元素可以看作是隔离了的独立容器，容器里面的元素不会在布局上影响到外面的元素，并且 BFC 具有普通容器所没有的一些特性。**

通俗一点来讲，可以把 BFC 理解为一个封闭的大箱子，箱子内部的元素无论如何翻江倒海，都不会影响到外部。

举例来说：因为BFC内部的元素和外部的元素绝对不会互相影响，因此， 当BFC外部存在浮动时，它不应该影响BFC内部Box的布局，BFC会通过变窄，而不与浮动有重叠。同样的，当BFC内部有浮动时，为了不影响外部元素的布局，BFC计算高度时会包括浮动的高度。避免margin重叠也是这样的一个道理。

### 4.1 如何触发 BFC？
只要元素满足下面任一条件即可触发 BFC 特性：

- body 根元素

- 浮动元素：float 除 none 以外的值

- 绝对定位元素：position (absolute、fixed)

- display 为 inline-block、table-cells、flex

- overflow 除了 visible 以外的值 (hidden、auto、scroll)

### 4.2 BFC 的特性与应用
- Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠。

- 父元素清楚子元素的浮动

- BFC 可以阻止元素被浮动元素覆盖

[link1](https://zhuanlan.zhihu.com/p/25321647)、[link2](https://blog.csdn.net/sinat_36422236/article/details/88763187)

## 5. px，rem，vw 区别
- vw : 1vw 等于视窗宽度的1%

- vh : 1vh 等于视窗高度的1%

- rem：font size of the root element。rem就是相对于根元素<html>的font-size来做计算，在某些机型上 font-size 设置有问题导致 rem 最终结果不符合预期导致样式错误。

## 6. 解释：BEM, scoped css, css modules 与 css-in-js
- BEM：让命名有规律、有含义，block 可视为模块，有一定作用域含义

- scoped css 限定 css 作用域，无关命名。无法适配多套主题

- css modules 使用算法命名，没有了命名冲突，也限定了 css 作用域。无法适配多套主题

- css-in-js 使用算法命名，拥有 css modules 的优势。同时将 css 视为 js 的字符串，赋予 css 更多能力

[link](https://www.jb51.net/css/742028.html)

## 7. 页面上弹窗的“滚动穿透”如何解决
- **小程序**

  - 使用 <page-meta /> 给 page 设置 overflow：hidden

  - 给遮罩层添加 catchtouchmove 的阻止。然后给弹窗套一层 <scroll-view />

  - 或者给页面套  <scroll-view /> 组件也可以

- **H5**

  - page 设置 overflow：hidden

  - touchmove + preventDefault：打开模态框时，阻止touchmove。缺陷： 模态框内的滚动内容失去滚动效果。

  - page body 设置 fixed 和 top，弹窗取消时，取消 fixed

  - better-scroll 或者 iscroll

相关链接：[link1](https://developers.weixin.qq.com/community/develop/article/doc/000886e3d182a8c8d00ca216e5fc13)、[link2](https://developers.weixin.qq.com/community/develop/article/doc/00046c19b2cff00af48a60bd05bc13)、[link3](https://juejin.cn/post/6844903766982918152)
