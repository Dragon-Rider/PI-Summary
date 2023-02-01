@import "base.less"

<link type="text/css" rel="stylesheet" href="/css/base.css"></link>

[TOC]

## 1.JS垃圾回收机制

浏览器的Javascript具有自动 <font class=orange>垃圾回收机制</font>(GC:Garbage Collecation)，垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存。

- **标记清除**: 在js中，最常用的垃圾回收机制是标记清除：当变量进入执行环境时，被标记为“进入环境”，当变量离开执行环境时，会被标记为“离开环境”。垃圾回收器会销毁那些带标记的值并回收它们所占用的内存空间。

- **谷歌浏览器**：“查找引用”，浏览器<font class=orange>不定时</font>去查找当前内存的引用，如果没有被占用了，浏览器会回收它；如果被占用，就不能回收。

- **IE浏览器**：“引用计数法”，当前内存被占用一次，计数累加1次，移除占用就减1，减到0时，浏览器就回收它。

优化手段：内存优化 ; 手动释放：取消内存的占用即可。<font class=orange>fn = null</font>


<style>
    .orange {
        color: orange;
        font: bold;
    }
    .red {
        color: red;
    }
</style>

