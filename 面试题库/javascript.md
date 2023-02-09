- [1.JS垃圾回收机制](#1js垃圾回收机制)
- [2. 闭包的是什么](#2-闭包的是什么)
  - [2.1 闭包的两大作用：保存/保护](#21-闭包的两大作用保存保护)
  - [2.2 解释一下 JavaScript 里的闭包，下面的代码执行结果是什么？](#22-解释一下-javascript-里的闭包下面的代码执行结果是什么)
- [3. JavaScript 数据类型有哪些？](#3-javascript-数据类型有哪些)
- [4. Javascript 中 null、NaN 和 undefined 的区别？](#4-javascript-中-nullnan-和-undefined-的区别)
- [5. JS 实现继承的方式](#5-js-实现继承的方式)
- [6. JS async,defer 对比](#6-js-asyncdefer-对比)
- [7.  EventLoop 和 作用域 考察](#7--eventloop-和-作用域-考察)
- [8.  setTimeout 替代 setInterval](#8--settimeout-替代-setinterval)
- [9. 解释下 JavaScript 中 Call 和 Apply 的区别](#9-解释下-javascript-中-call-和-apply-的区别)
- [10. 解释下事件代理](#10-解释下事件代理)
- [11. for in, for of, Object.keys 的区别](#11-for-in-for-of-objectkeys-的区别)
- [12. js判断空对象的几种方法?](#12-js判断空对象的几种方法)
- [13. js判断对象相等的几种方法?](#13-js判断对象相等的几种方法)
- [14. Array.prototype.reduce() 的考察](#14-arrayprototypereduce-的考察)
- [15. JS 对象转换的考察](#15-js-对象转换的考察)
- [16. 对象深拷贝方法](#16-对象深拷贝方法)
- [17. 执行机制, 同步异步, Event Loop, 宏任务, 微任务](#17-执行机制-同步异步-event-loop-宏任务-微任务)
- [18. JS 内存空间理解](#18-js-内存空间理解)
  - [18.1 heap, stack, queue](#181-heap-stack-queue)
  - [18.2 基础类型 和 引用类型](#182-基础类型-和-引用类型)

@import "/css/base.less"
<!--
    注释：在支持完整 markdown 的情况下，用彩色是更方便的写法。但是考虑到以后的开源情况和方便书写，将彩色的写法用 `` 取代
-->
<!--
<link type="text/css" rel="stylesheet" href="/css/base.css"></link>
-->
<!--
<style>
    .orange {
        color: orange;
        font: bold;
    }
    .red {
        color: red;
    }
</style>
-->
## 1.JS垃圾回收机制

浏览器的Javascript具有自动 <font class=orange>垃圾回收机制</font>(GC:Garbage Collecation)，垃圾收集器会定期（周期性）找出那些不在继续使用的变量，然后释放其内存。

- **标记清除**: 在js中，最常用的垃圾回收机制是标记清除：当变量进入执行环境时，被标记为“进入环境”，当变量离开执行环境时，会被标记为“离开环境”。垃圾回收器会销毁那些带标记的值并回收它们所占用的内存空间。

- **谷歌浏览器**：“查找引用”，浏览器<font class="orange">不定时</font>去查找当前内存的引用，如果没有被占用了，浏览器会回收它；如果被占用，就不能回收。

- **IE浏览器**：“引用计数法”，当前内存被占用一次，计数累加1次，移除占用就减1，减到0时，浏览器就回收它。

优化手段：内存优化 ; 手动释放：取消内存的占用即可。<font class=orange>fn = null</font>


## 2. 闭包的是什么
在 JS 中变量的作用域属于`函数作用域`， 在函数执行完后，作用域就会被清理，内存也会随之被回收。

但是由于闭包函数是建立在函数内部的子函数， 由于其可访问上级作用域，即使上级函数执行完，`作用域`也不会随之销毁， 这时的子函数(也就是闭包)，便拥有了访问上级作用域中变量的权限，即使上级函数执行完后作用域内的值也不会被销毁。

[link1](https://juejin.cn/post/7016593221815910408)、[link2](https://www.cnblogs.com/mzwr1982/archive/2012/05/20/2509295.html)、[link3](https://www.zhihu.com/question/19554716)、[link4](https://www.jb51.net/article/24101.htm)

### 2.1 闭包的两大作用：保存/保护

- **保护**：划分一个独立的代码执行区域，在这个区域中有自己私有变量存储的空间，`保护`自己的`私有变量`不受外界干扰（操作自己的私有变量和外界没有关系）；

- **保存**：如果当前上下文不被释放【只要上下文中的某个东西被外部占用即可】，则存储的这些`私有变量`也不会被释放，可以供其下级上下文中调取使用，相当于把一些值`保存`起来了；

[掘金](https://juejin.cn/post/7016593221815910408)

### 2.2 解释一下 JavaScript 里的闭包，下面的代码执行结果是什么？
闭包的特性：

- 作用域链

- 垃圾(内存)回收机制

- 函数嵌套

闭包的作用：1. 在内存中`维持一个变量`；2. 通过`保护变量`的安全实现JS私有属性和私有方法。

闭包的用途：之所以可能通过这种方式在 JavaScript 种实现公有，私有，特权变量正是因为闭包，闭包是指在 JavaScript中，内部函数总是可以访问其所在的外部函数中声明的参数和变量，即使在其外部函数被返回（寿命终结）了之后。
```
// 闭包的考题，也可以是块级作用域的考题；
var result = [];

function foo() {
    for (var i = 0; i < 3; i++) {
        result[i] = function() {
            console.log(i);
        }
    }
}

foo();

result[0]();
result[1]();
result[2]();

// 输出：
// 3
// 3
// 3

// 1. 闭包改造方法，输出 0，1，2
var result = [];

function foo() {
  for (var i = 0; i < 3; i++) {
    result[i] = function(j) {
      return () => {
        console.log(j);
      }
    }(i)
  }
}
foo();
result[0]();
result[1]();
result[2]();


// 2. 块级作用域改造方法，输出 0，1，2
var result = [];

function foo() {
    for (let i = 0; i < 3; i++) {
        result[i] = function() {
            console.log(i);
        }
    }
}

foo();

result[0]();
result[1]();
result[2]();
// 输出：
// 0
// 1
// 2
```

引申手写一个闭包：
```
// 闭包使用的例子：
var foo = function() {
    var secret = 'secret';
    // “闭包”内的函数可以访问 secret 变量，而 secret 变量对于外部却是隐藏的
    return {
        get_secret: function () {
            // 通过定义的接口来访问 secret
            return secret;
        },
        new_secret: function (new_secret) {
            // 通过定义的接口来修改 secret
            secret = new_secret;
        }
    };
}();

foo.get_secret (); // 得到 'secret'
foo.secret; // Type error，访问不能
foo.new_secret ('a new secret'); // 通过函数接口，我们访问并修改了 secret 变量
foo.get_secret (); // 得到 'a new secret'
```

## 3. JavaScript 数据类型有哪些？

- 基本类型：string，number，bigint，boolean，null，undefined，symbol。在栈内存中，存的值。

- 引用类型：Object，包括：Array - 数组对象、Function - 函数对象 等、RegExp - 正则对象、Date - 日期对象、Math - 数学函数；在内存中，存的引用地址。

![alt 数据类型](https://github.com/Dragon-Rider/eragon.github.io/raw/main/imgs/JavaScript%20%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E6%9C%89%E5%93%AA%E4%BA%9B.jpg "数据类型1")

## 4. Javascript 中 null、NaN 和 undefined 的区别？
- 未定义的值和定义未赋值的为 undefined，null是一种特殊的 object, NaN是一种特殊的 number。

- undefined与null是相等（==）；NaN与任何值都不相等，与自己也不相等；

## 5. JS 实现继承的方式
- 原型链继承：将父类的实例作为子类的原型

- 构造函数继承：constructor 用 call / apply

- clase extend：ES6 继承的方式

## 6. JS async,defer 对比
- **async**：异步发送 JS 资源请求，但如果 html 还没解析完毕，也会直接解析 JS 脚本

- **defer（更好）**：异步发送 JS 资源请求，等 html 解析完毕后，再解析 JS 脚本
`常考题`

## 7.  EventLoop 和 作用域 考察
```
// 结果：1s 后立即输出 5 个 5；因为 for 循环了五次，所以 setTimeout 被 5 次添加到时间循环中，等待一秒后全部执行。
for (var i = 0; i < 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
```
[掘金](https://juejin.cn/post/6989055903651594248)

## 8.  setTimeout 替代 setInterval
用 JavaScript 写个定时器，规定某段代码隔一段时间执行一次，为什么用 setTimeout 替代 setInterval ?

setInterval 缺点：

- 使用 setInterval 时，某些间隔会被跳过；

- 可能多个定时器会连续执行；

可以这么理解：每个 setTimeout 产生的任务会直接 push 到任务队列中；而 setInterval 在每次把任务 push 到任务队列前，都要进行一下判断(看上次的任务是否仍在队列中，如果有则不添加，没有则添加)。

再次强调，定时器指定的时间间隔，表示的是何时将定时器的代码`添加到消息队列`，而不是何时执行代码。所以真正何时执行代码的时间是不能保证的，取决于何时被主线程的事件循环取到，并执行。
[link1](https://juejin.cn/post/6844903971962765326)、[link2](https://juejin.cn/post/6989055903651594248)

## 9. 解释下 JavaScript 中 Call 和 Apply 的区别
JavaScript中的每一个Function对象都有一个apply()方法和一个call()方法，它们的语法分别为：
```
/*apply()方法*/
function.apply(thisObj[, argArray])

/*call()方法*/
function.call(thisObj[, arg1[, arg2[, [,...argN]]]]);
```
- call：调用一个对象的一个方法，以另一个对象替换当前对象。例如：B.apply(A, arguments);即A对象应用B对象的方法。

- apply：同上，但参数为数组或者 arguments 对象。例如：B.call(A, args1, args2);即A对象调用B对象的方法。

```
// 实现继承
function Animal(name){
  this.name = name;
  this.showName = function(){
        alert(this.name);
    }
}

function Cat(name){
  // call的用法
  Animal.call(this,name);
  // apply的用法
  // Animal.apply(this,[name]);
}

var cat = new Cat("咕咕");
cat.showName();
```
[link](https://www.cnblogs.com/lengyuehuahun/p/5643625.html)

## 10. 解释下事件代理
事件代理即`事件委托`，JavaScript高级程序设计上讲：事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以`管理某一类型的所有事件`。那这是什么意思呢？网上的各位大牛们讲事件委托基本上都用了同一个例子，就是取快递来解释这个现象。

- 第一，现在委托前台的同事是可以代为签收的，即程序中的现有的dom节点是有事件的；

- 第二，新员工也是可以被前台MM代为签收的，即程序中`新添加的dom节点也是有事件的`。

[link](https://www.cnblogs.com/liugang-vip/p/5616484.html)

## 11. for in, for of, Object.keys 的区别
- 一句话概括：for in 一般用来遍历对象（object）的key、for of 一般用来遍历数组（array）的value。

- for in 是 ES5；for of 是 ES6

- for of 不可以遍历普通的对象，只能遍历可迭代对象（包括Array，Map，Set，String，TypedArray，arguments 对象等等）

- for in 会遍历出来可枚举属性（包括它的原型链上的可枚举属性），Object.keys() 只会遍历自身的属性

- 备注： [Object.getOwnPropertyNames()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames)  方法(ES6)返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括Symbol值作为名称的属性，不包括原型链上的属性）组成的数组。

[link1](https://segmentfault.com/a/1190000022348279)、[link2](https://segmentfault.com/q/1010000006658882)、[link3](https://blog.csdn.net/weixin_45000381/article/details/96996942)

## 12. js判断空对象的几种方法?
- 将对象转为字符串比较：JSON.stringify(a)=="{}"

- for…in循环

- getOwnPropertyNames方法：Object.getOwnPropertyNames(obj).length == 0

- getOwnPropertyNames 方法返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括Symbol值作为名称的属性）组成的数组。

- Object.keys()：Object.keys(data).length === 0

[link](https://blog.csdn.net/watercatmiao/article/details/84261015)

## 13. js判断对象相等的几种方法?
- 方法一：使用 JSON.stringify()方法，将对象转化为字符串。然后使用 “===” 判断即可：

  优点：用法简单，对于顺序相同的两个对象可以快速进行比较得到结果

  缺点：这种方法有限制就是当两个对比的对象中key的顺序不是完全相同时会比较出错

```
let obj1 = {a:1, b:2};
let obj2 = {a:1, b:2};

console.log(JSON.stringify(obj1) === JSON.stringify(obj2));
```

- 方法二：Object.getOwnPropertyNames()，只要两个对象的名和键值都相同。那么两个对象的内容就相同了

  优点：相对方法一进行了优化，可以应对不同顺序的Object进行比较，不用担心顺序不同而对比出错

  缺点：从方法中可以看到只能获取到第一层的key组成的数组，当对象是复合对象时无法进行多层对象的比较
```
function isObjectValueEqual(a, b) {
  var aProps = Object.getOwnPropertyNames(a);
  var bProps = Object.getOwnPropertyNames(b);

  if (aProps.length != bProps.length) return false;

  for (propName in a) {
    // return 直接从函数里退出了；break 只是从 for 循环退出；continue 从当前循环的值退出，执行循环的下一个值；
    if ( a[propName] !== b[propName]) {
      return false;
    }
  }
  return true;
}
```

- 方法三：递归的方法判断（参考：JS判断两个对象内容是否相等）
```
function isObjectValueEqual(a, b) {
  var aProps = Object.getOwnPropertyNames(a);
  var bProps = Object.getOwnPropertyNames(b);
  if (aProps.length != bProps.length) {
    return false;
  }
  for (var i = 0; i < aProps.length; i++) {
    var propName = aProps[i];
    // 故先判断两边都有相同键名
    if (!b.hasOwnProperty(propName)) return false;

    var propA = a[propName];
    var propB = b[propName];

    if (propA instanceof Object) {
      if (!this.isObjectValueEqual(propA, propB)) {
        return false;
      }
    } else if (propA !== propB) {
      return false;
    }
  }
  return true;
}

var a = {c: { age: 3 }, id: 3, name: 1 };
var b = {c: { age: 3 }, id: 3, name: 2 };

console.log(isObjectValueEqual(a,b));
```

## 14. Array.prototype.reduce() 的考察
要求：将 string('a-b-c-d') 分割为数组[a, b, c, d]，用reduce拼接到newStr中，得到的结果 0abcd；
```
let string = 'a-b-c-d'
let arr = string.split('-')//[a,b,c,d]
let newStr = '0'

function getString(all, currentValue, currentIndex, arr) {
    console.log(all, currentValue, currentIndex)
    return all += currentValue
}
let value = arr.reduce(getString,newStr)
console.log(value) // 0abcd
```
[link1](https://blog.csdn.net/qq_43441476/article/details/105559561)、[link2](https://www.jianshu.com/p/e375ba1cfc47)

## 15. JS 对象转换的考察
要求：写一个方法实现，任意对象作为入参传入，只能返回对象上特定的几个值(a, b, c)，尽可能的简单，高效。
```
// 举例
// 入参：{a:1, b:2, c:3}, 函数返回值: {a:1, b:2, c:3}
// 入参：{a:1, b:2, d:4}, 函数返回值: {a:1, b:2}
// 入参：{a:1, b:2}, 函数返回值: {a:1, b:2}

// 方法1: 遍历 obj, 这个时间复杂度更高。
function transform(obj, keyList) {
  if (typeof obj !== 'object') return null;
  const result = {};

	for(key in obj) {
    if (obj.hasOwnProperty('key') && (keyList.includes(key)){
    	result[key] = objkey[key]
    }
  }
  return result;
}

// 方法2:  遍历 keyList，这个时间复杂度更低。
function transform(obj, keyList) {
  if (typeof obj !== 'object') return null;

  const result = {};

  keyList.forEach(item) {
    if (obj.hasOwnProperty(item)) {
      result[key] = objkey[key]
    }
  }

  return result;
}
```

## 16. 对象深拷贝方法
```
JSON.parse(JSON.stringify(obj))
```
JSON.parse(JSON.stringify 的问题：

- 如果obj⾥⾯存在时间对象，JSON.parse(JSON.stringify(obj))之后，时间对象变成了字符串。

- 如果obj⾥有函数，undefined，则序列化的结果会把函数， undefined丢失。

- JSON.stringify()只能序列化对象的可枚举的⾃有属性。如果obj中的对象是有构造函数⽣成的，则使⽤JSON.parse(JSON.stringify(obj))。深拷贝后，会丢弃对象的constructor。

- 如果对象中存在循环引⽤的情况也⽆法正确实现深拷贝。
[参考](https://wenku.baidu.com/view/704ab840aa8271fe910ef12d2af90242a895ab39.html?_wkts_=1675936522410)

## 17. 执行机制, 同步异步, Event Loop, 宏任务, 微任务
- js是单线程, 也就是说只有一个 主线程执行，同步代码可以阻塞异步队列的直行。待同步代码直行完毕后再执行异步代码。
  - JS为什么单线程?
    js是浏览器端的,主要是操作DOM使用。

    浏览器操作，如果不是单线程，DOM操作会乱掉，有很多个人操作，同时删除修改新增什么的，都不明确。

    不如一个人指挥操作。

    创建多线程环境: 主线程做自己的事儿DOM，子线程不得处理UI, 做其他的事儿，结果交给主线程。 现在像web worker 是有主线程和 创建子线程的。

    - Web worker: 目标是减轻进程工作任务密集。

    - Service worker: 浏览器和网络间的代理 离线目标。

    - Worklet: 浏览器渲染 HOOK
- 优先级: 同步任务 > Micro Event Queue 异步微任务队列 > Macro Event Queue 异步宏任务队列
  - 宏任务 macro task: script代码, setTimeout, setInterval
  - 微任务 micro task: Promise.then, process.nextTick
  - EventLoop：本质上是js对异步处理机制。先执行同步，再执行微任务，最后执行宏任务。有上述过程不断重复，一旦发现优先级比自己高的, 要去执行高的, 让出主线程, 等前一个优先级任务空了, 再执行后面的。叫作 事件循环机制。
![alt Event Loop](https://github.com/Dragon-Rider/PI-Summary/raw/master/imgs/event-loop.png "Event Loop")

```
// EventLoop 测试题1
setTimeout(function(){
    console.log('定时器开始啦')
},0);

new Promise(function(resolve){
    console.log('马上执行for循环啦');
    for(var i = 0; i < 10000; i++){
        i == 99 && resolve();
    }
}).then(function(){
    console.log('执行then函数啦')
});

console.log('代码执行结束');

async function async1() {
  await async2() // [这个注释不能给候选人]await = 立即执行，后面的code, 相当于放到promise.then(...)
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end')
}
async1();

/*
马上执行for循环啦
代码执行结束
async2 end
执行then函数啦
async1 end
定时器开始啦
*/
```

## 18. JS 内存空间理解
1. 变量对象 和 堆内存

- 基本类型 保存在 栈stack内存中

- 引用类型值 保存在 堆heap内存中

- 事件循环机制 是在队列queue中

- 闭包引用会 保存在 堆heap内存中

### 18.1 heap, stack, queue
- 堆 heap: 类似kv, 我们知道key, 就能获取到value。

- 栈 stack: 栈空间先进后出，后进先出的特点。

- 队列 queue: 先进先出, Event Loop事件循环应用。

### 18.2 基础类型 和 引用类型
- 基础类型: Undefined, Null, Boolean, Number, String.

- 引用类型: Object, Array....

![JS Data Structure](https://github.com/Dragon-Rider/PI-Summary/raw/master/imgs/js_data_structure.png "JS Data Structure")
