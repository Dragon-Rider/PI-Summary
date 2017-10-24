## SAP技术问题汇总
### 1. jQuery的.bind()、.live()和.delegate()之间区别？

#### bind()  
`
　　$('a').bind('click', function() { alert("That tickles!") });
`

这是最简单的绑定方法了。JQuery扫描文档找出所有的$(‘a’)元素，并把alert函数绑定到每个元素的click事件上。

#### live()
`
　　$('a').live('click', function() { alert("That tickles!") });
`

JQuery把alert函数绑定到$(document)元素上，并使用’click’和’a’作为参数。任何时候只要有事件冒泡到document节点上，它就查看该事件是否是一个click事件，以及该事件的目标元素与’a’这一CSS选择器是否匹配，如果都是的话，则执行函数。

#### delegate()
`
　　$('#container').delegate('a', 'click', function() { alert("That tickles!") });
`

Query扫描文档查找$(‘#container’)，并使用click事件和’a’这一CSS选择器作为参数把alert函数绑定到$(‘#container’)上。任何时候只要有事件冒泡到$(‘#container’)上，它就查看该事件是否是click事件，以及该事件的目标元素是否与CCS选择器相匹配。如果两种检查的结果都为真的话，它就执行函数。

#### 参考文档
+ [jQuery的.bind()、.live()和.delegate()之间区别](http://kb.cnblogs.com/page/94469/)



