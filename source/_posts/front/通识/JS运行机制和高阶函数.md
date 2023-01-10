---
title: JS高阶函数和运行机制
cover: https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/BgImage/19.png
categories: 
- [前端,通识]
tags:
- 高阶函数
- JavaScript
---

# JS高阶函数和运行机制

# 🗽高阶函数

高阶函数英文叫Higher-order function。那么什么是高阶函数？



下面我们通过一个小demo来认识一下，到底什么是高阶函数吧。👀

```javascript
function add(x,y,fn){
   return fn(x) + fn(y);
}
//当我们调用add(-5,6,Math.abs)时，我们不难发现，函数的执行过程为
x = -5;
y = 6;
f = Math.abs;
f(x) + f(y) ==> Math.abs(-5) + Math.abs(6) ==> 11;
return 11;
```

用最简单的话来说，高阶函数就是一个将函数作为参数或者返回值的函数。



例如`Array.prototype.map`, `Array.prototype.filter`和`Array.prototype.reduce`是JavaScript原生的高阶函数。



那看完上面的东西，大家对于高阶函数有没有什么理解了呢？没有关系，接着往下看吧。



以下`fn`就是一个高阶函数。

```javascript
// 作为参数传递
function fn(callback){
    callback && callback()
}
fn(function(){console.log("Hi")}
```

把函数当作参数传递，代表可以抽离出一部分容易变化的业务逻辑，把这部分业务逻辑放在函数参数中，这样一来可以分离业务代码中变化与不变的部分。

```javascript
// 作为返回值输出
function fn(){
    return function(){}
}
fn()
```

相比把函数当作参数传递，函数当作返回值输出的应用场景也有很多。让函数继续返回一个可执行的函数，意味着运算过程是可延续的。

## 高阶函数的具体例子

### 1.回调函数

大家可能现在都不用`xhr`了，在路哥的推荐下，相信越来越多的同学已经开始使用`fetch`，对`fetch`也愈发熟练。但我们今天的主角依然是我们的`xhr`。



额怕大家忘了原生ajax咋用我还特地回去我们那届的课件给你们截了个图

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646740338446-8b723414-afc2-4f00-bd9e-e82f7cc536b5.png)



下面我将尝试着给大家封装一个自己的ajax

```javascript
function myAjax(options={
  type: 'get',
  url: '',
  async: true,
  data: {},
  header: {
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  success: function() {},
  error: function() {}
}) {
  let xhr;
  if(window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
  }else{
    xhr = new ActiveXObject('Microsoft.XMLHTTP');
  }
  let params = '';
  for(let key in options.data) {
    params += key + '=' + options.data[key] + '&';
    params = params.substring(0, params.length - 1);
  }
  xhr.open(options.type, options.url, options.async);
  switch(options.type) {
    case 'get':{
      xhr.open('get', options.url + '?' + params, options.async);
      break;
    }
    case 'post':{
      xhr.setRequestHeader('Content-Type', options.header['Content-Type']);
      if(options.header['Content-Type'] === 'application/x-www-form-urlencoded') {
        // xhr.open('post', options.url, options.async);
        xhr.send(params);
      }
      if(options.header['Content-Type'] === 'application/json') {
        // xhr.open('post', options.url, options.async);
        xhr.send(JSON.stringify(options.data));
      }else{
        // xhr.open('post', options.url, options.async);
        xhr.send(options.data);
      }
      break;
    }
      
  }
  xhr.onreadystatechange = function() {
    if(xhr.readyState === 4) {
      if(xhr.status === 200) {
        options.success(xhr.responseText);
      }else{
        options.error(xhr.status);
      }
    }
  }
}
//额如果需要注释的话，等我课后加上去吧。
```

在`ajax`异步请求的应用中，回调函数的使用非常频繁。想在`ajax`请求返回之后做一些事情，但又并不知道请求返回的确切时间时，最常见的方案就是把callback函数当作参数传入发起`ajax`请求的方法中，待请求完成之后执行callback函数。



回调函数的应用不仅只在异步请求中，当一个函数不适合执行一些事件时，也可以把这些事件封装成一个函数，并把它作为参数传递给另外一个函数。

### 2.偏函数

就是把一个函数的某些参数先固化，也就是设置默认值，返回一个新的函数，在新函数中继续接收剩余参数，这样调用这个新函数会更简单。

下面是使用`Object.prototype.toString`方法判断数据类型的一系列的`isType`函数：

```javascript
const isString = function( obj ){
  return Object.prototype.toString.call( obj ) === '[object String]';
};
const isArray = function( obj ){
  return Object.prototype.toString.call( obj ) === '[object Array]';
};
const isNumber = function( obj ){
  return Object.prototype.toString.call( obj ) === '[object Number]';
};
```

可简化为：

```javascript
function isType(type) {
    return function(obj) {
        return Object.prototype.toString.call(obj) === `[object ${type}]`
    }
}

const isArray = isType('Array');
const isString = isType('String');
console.log(isArray([1, 2, [3,4]])); // true
console.log(isString({}));           // false
```

### 3.预置函数

当达到条件时再执行回调函数

```javascript
function after(time, cb) {
    return function() {
        if (--time === 0) {
            cb();
        }
    }
}
// 吃三碗才能吃饱
let eat = after(3, function() {
    console.log('吃饱了');
});
eat();
eat();
eat();
// eat函数只有执行3次的时候才会输出'吃饱了'
```

### 4.函数柯里化

[柯里化（Currying）](https://en.wikipedia.org/wiki/Currying)是一种关于函数的高阶技术。它不仅被用于 JavaScript，还被用于其他编程语言。



柯里化是一种函数的转换，它是指将一个函数从可调用的 f(a, b, c) 转换为可调用的 f(a)(b)(c)。

柯里化不会调用函数。它只是对函数进行转换。



让我们先来看一个例子，以更好地理解我们正在讲的内容，然后再进行一个实际应用。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646745318056-579027f8-82da-47db-945c-96451c730aa7.png)

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646746239022-bf201de8-973b-4c86-bd74-fad523aa6881.png)

```javascript
const currying = function(fn){
    let args = [];
    return function(){
        if(arguments.length===0){
            return fn.apply(this,args)
        }else{
            [].push.apply(args,arguments);
            return arguments.callee//他可以引用该函数的函数体内当前正在执行的函数
        }
    }
};     
let cost = (function(){
    let money = 0;
    return function(){
        for(let i = 0,l=arguments.length;i<l;i++){
           money += arguments[i]
        }
        return money;
    }
})()

let sum =currying(cost)

cost(100)
cost(200)
cost(300)

console.log(sum());
```

### 5.函数防抖

[可视化展示节流和防抖](http://demo.nimius.net/debounce_throttle/)

函数防抖，就是指触发事件后，在 n 秒后只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数的执行时间。

简单的说，当一个动作连续触发，只执行最后一次。



打个比方，坐公交，司机需要等最后一个人进入才能关门。每次进入一个人，司机就会多等待几秒再关门。

```javascript
const debounce = (func, wait) => {
  //定义一个计时器
  let timer;
  return () => {
    clearTimeout(timer);
    timer = setTimeout(func, wait);
  };
};


function debounce(fn, delay = 500) {
    // timer 是在闭包中的
    let timer = null;
    
    return function() {
        if (timer) {
            clearTimeout(timer)
        }
        timer = setTimeout(() => {
            fn.apply(this, arguments)
            timer = null
        }, delay)
    }
}

// test debounce 返回一个函数
input1 = document.getElementById('input1')
input1.addEventListener('keyup', debounce(function () {
    console.log(input1.value)
}, 600))
```

### 6.函数节流

限制一个函数在一定时间内只能执行一次

举个例子，乘坐地铁，过闸机时，每个人进入后3秒后门关闭，等待下一个人进入。

为了方便理解，我们首先通过一个[可视化的工具](http://demo.nimius.net/debounce_throttle/)，感受一下三种环境（正常情况、函数防抖情况 debounce、函数节流 throttle）下，对于mousemove事件回调的执行情况。

```javascript
const throttle =function(fn,interval){
    let _self =fn, //保存需要被延迟执行的函数引用
    timer, //定时器
    firstTime = true;//是否为第一次调用
    return function(){
        let args =arguments;
        _me = this;
        console.log(_me);
        if(firstTime){
            _self.apply(_me, args);
            return firstTime =false;
        }
        if(timer){//如果定时器还在，则说明前一次延迟执行还没有完成
            return false;
        }
        timer=setTimeout(function(){
            clearTimeout(timer);
            timer = null;
            _self.apply(_me, args);
        },interval||500)
    }
}

window.onresize = throttle(function(){
    console.log(1);
},2000);
```



## 💡运行机制



说起运行机制，大家可能都不陌生，或多或少已经在代码中见过了他的身影，但是大家有真正的去了解过他吗。今天我们就深入la



- 首先我们要了解进程和线程的概念
- 其次我们要知道浏览器的进程线程常识
- 再然后通过Event Loop、宏任务(macrotask)微任务(microtask)来看浏览器的几个线程间是怎样配合的
- 再然后通过例子来印证我们的猜想
- 最后提下NodeJS的运行机制

### 进程与线程

#### 在进程和线程上执行程序

在深入到浏览器的架构之前我们还得了解一下进程（process）和线程（thread）的相关概念。进程可以看成正在被执行的应用程序（executing program）。而**线程是跑在进程里面的**，一个进程里面可能有一个或者多个线程，这些线程可以执行任何一部分应用程序的代码。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883359400-14a25c07-a8b6-468b-9778-38b393a3acc2.png)

进程就像一个大鱼缸，而线程就是浴缸里面畅游的鱼儿

当你启动一个应用程序的时候，操作系统会为这个程序创建一个进程同时还为这个进程分配一片**私有的内存空间**，这片空间会被用来存储所有程序相关的数据和状态。当你关闭这个程序的时候，这个程序对应的进程也会随之消失，进程对应的内存空间也会被操作系统释放掉。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883359491-cbacc83f-53a6-4d34-8767-20d33b5a6e7a.png)

进程使用系统分配的内存空间去存储应用的数据

有时候为了满足功能的需要，创建的进程会叫系统创建另外一些进程去处理其它任务，不过新建的进程会拥有**全新的独立的内存空间**而不是和原来的进程共用内存空间。如果这些进程需要通信，它们要通过**IPC**机制（Inter Process Communication）来进行。很多应用程序都会采取这种**多进程的方式**来工作，因为进程和进程之间是互相独立的它们互不影响，换句话来说，如果其中一个工作进程（worker process）挂掉了其他进程不会受到影响，而且挂掉的进程还可以重启。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883359501-7305cdb3-a53f-4c21-9433-f275b9cec023.png)

为了更好地在本系列文章中展开论述，我们主要讨论最新的Chrome浏览器架构，它采用的是**多进程架构**，以下是架构图：

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883479536-7e32eed9-0e3b-46b0-8462-520bce730352.png)

Chrome的多进程架构图，多个渲染进程的卡片（render process）是用来表明Chrome会为每一个tab创建一个渲染进程。

Chrome浏览器会有一个浏览器进程（browser process），这个进程会和其他进程一起协作来实现浏览器的功能。对于渲染进程（renderer process），Chrome会尽可能为每一个tab甚至是页面里面的每一个iframe都分配一个单独的进程。

#### 各个进程如何分工合作呢？

以下是各个进程具体负责的工作内容：

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883479499-fe8910b5-ff98-4921-b51c-05c72894ef8a.png)

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883479566-790b4007-87b4-4ee2-93e2-0d63b8edc5c4.png)

不同的进程负责浏览器不同部分的界面内容

除了上面列出来的进程，Chrome还有很多其他进程在工作，例如扩展进程（Extension Process）和工具进程（utility process）。如果你想看一下你的Chrome浏览器现在有多少个进程在跑可以点击浏览器右上角的更多按钮，选择更多工具和任务管理器：

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646883479599-b5d59fea-3530-4d14-9c3c-83bfcc677c78.png)

在弹出的窗口里面你会看到正在工作的进程列表，以及每个进程使用的CPU和内存状况。

哎呀我去，好像扯远了！！！！



呃呃呃，回归正题，我们今天来学一下我们js的运行机制



换句话说：
做个简单的比喻：进程=火车，线程=车厢

- 线程在进程下行进（单纯的车厢无法运行）
- 一个进程可以包含多个线程（一辆火车可以有多个车厢）
- 不同进程间数据很难共享（一辆火车上的乘客很难换到另外一辆火车，比如站点换乘）
- 同一进程下不同线程间数据很易共享（A车厢换到B车厢很容易）
- 进程要比线程消耗更多的计算机资源（采用多列火车相比多个车厢更耗资源）
- 进程间不会相互影响，一个线程挂掉将导致整个进程挂掉（一列火车不会影响到另外一列火车，但是如果一列火车上中间的一节车厢着火了，将影响到所有车厢）
- 进程可以拓展到多机，进程最多适合多核（不同火车可以开在多个轨道上，同一火车的车厢不能在行进的不同的轨道上）
- 进程使用的内存地址可以上锁，即一个线程使用某些共享内存时，其他线程必须等它结束，才能使用这一块内存。（比如火车上的洗手间）－"互斥锁"
- 进程使用的内存地址可以限定使用量（比如火车上的餐厅，最多只允许多少人进入，如果满了需要在门口等，等有人出来了才能进去）－“信号量”（自己的理解，如果有不对的地方多多理解）

### 浏览器又是如何渲染的呢？

首先呢，我们先来了解一下内核（渲染进程）中一些主要的线程：



- GUI渲染线程

- - 负责渲染浏览器界面，解析HTML，CSS，构建DOM树和RenderObject树，布局和绘制等。
  - 当界面需要重绘（Repaint）或由于某种操作引发回流(reflow)时，该线程就会执行
  - 注意，**GUI渲染线程与JS引擎线程是互斥的**，当JS引擎执行时GUI线程会被挂起（相当于被冻结了），GUI更新会被保存在一个队列中**等到JS引擎空闲时**立即被执行。



- JS引擎线程

- - 也称为JS内核，负责处理Javascript脚本程序。（例如V8引擎）
  - JS引擎线程负责解析Javascript脚本，运行代码。
  - JS引擎一直等待着任务队列中任务的到来，然后加以处理，一个Tab页（renderer进程）中无论什么时候都只有一个JS线程在运行JS程序
  - 同样注意，**GUI渲染线程与JS引擎线程是互斥的**，所以如果JS执行的时间过长，这样就会造成页面的渲染不连贯，导致页面渲染加载阻塞。



- 事件触发线程

- - 归属于浏览器而不是JS引擎，用来控制事件循环（可以理解，JS引擎自己都忙不过来，需要浏览器另开线程协助）
  - 当JS引擎执行代码块如setTimeOut时（也可来自浏览器内核的其他线程,如鼠标点击、AJAX异步请求等），会将对应任务添加到事件线程中
  - 当对应的事件符合触发条件被触发时，该线程会把事件添加到待处理队列的队尾，等待JS引擎的处理
  - 注意，由于JS的单线程关系，所以这些待处理队列中的事件都得排队等待JS引擎处理（当JS引擎空闲时才会去执行）



- 定时触发器线程

- - `setInterval`与`setTimeout`所在线程
  - 浏览器定时计数器并不是由JavaScript引擎计数的,（因为JavaScript引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确）
  - 因此通过单独线程来计时并触发定时（计时完毕后，添加到事件队列中，等待JS引擎空闲后执行）
  - 注意，W3C在HTML标准中规定，规定要求setTimeout中低于4ms的时间间隔算为4ms。



- 异步http请求线程

- - 在XMLHttpRequest在连接后是通过浏览器新开一个线程请求
  - 将检测到状态变更时，如果设置有回调函数，异步线程就**产生状态变更事件**，将这个回调再放入事件队列中。再由JavaScript引擎执行。

渲染进程负责标签（tab）内发生的所有事情。在渲染进程里面，主线程（main thread）处理了绝大多数你发送给用户的代码。如果你使用了web worker或者service worker，相关的代码将会由工作线程（worker thread）处理。合成（compositor）以及光栅（raster）线程运行在渲染进程里面用来高效流畅地渲染出页面内容。

**渲染进程的主要任务是将HTML，CSS，以及JavaScript转变为我们可以进程交互的网页内容**。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646885584456-c236de15-c333-4827-9806-952b24beb40f.png)

渲染进程里面有：一个主线程（main thread），几个工作线程（worker threads），一个合成线程（compositor thread）以及一个光栅线程（raster thread）

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887122710-cfc41229-cb8e-4e0b-873f-1902ba3f41ed.webp)

浏览器渲染过程大体分为如下三部分：

#### 1）浏览器会解析三个东西：

- 一是HTML/SVG/XHTML，HTML字符串描述了一个页面的结构，浏览器会把HTML结构字符串解析转换DOM树形结构。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887122705-5c1b604c-0764-471d-bc30-5d341feacecf.webp)

- 二是CSS，解析CSS会产生CSS规则树，它和DOM结构比较像。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887122716-9d08a116-0625-43eb-80e4-5ba171cf221d.webp)

- 三是Javascript脚本，等到Javascript 脚本文件加载后， 通过 DOM API 和 CSSOM API 来操作 DOM Tree 和 CSS Rule Tree。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887122698-60158a05-7381-4ac3-ae30-3fab85f7939b.webp)

#### 2）解析完成后，浏览器引擎会通过DOM Tree 和 CSS Rule Tree 来构造 Rendering Tree。

- Rendering Tree 渲染树并不等同于DOM树，渲染树只会包括需要显示的节点和这些节点的样式信息。
- CSS 的 Rule Tree主要是为了完成匹配并把CSS Rule附加上Rendering Tree上的每个Element（也就是每个Frame）。
- 然后，计算每个Frame 的位置，这又叫layout和reflow过程。

#### 3）最后通过调用操作系统Native GUI的API绘制。

接下来我们针对这其中所经历的重要步骤详细阐述

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887447481-6e96f195-2ead-4646-a695-984e4d03533e.png)

### 几点补充说明

#### 1.async和defer的作用是什么？有什么区别?

接下来我们对比下 defer 和 async 属性的区别：

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887863753-9d51d776-ea89-412c-be51-7cd9695e9ae1.webp)

其中蓝色线代表JavaScript加载；红色线代表JavaScript执行；绿色线代表 HTML 解析。

1. **情况1<script src="script.js"></script>**

没有 defer 或 async，浏览器会立即加载并执行指定的脚本，也就是说不等待后续载入的文档元素，读到就加载并执行。

1. **情况2<script async src="script.js"></script> (异步下载)**

async 属性表示异步执行引入的 JavaScript，与 defer 的区别在于，如果已经加载好，就会开始执行——无论此刻是 HTML 解析阶段还是 DOMContentLoaded 触发之后。需要注意的是，这种方式加载的 JavaScript 依然会阻塞 load 事件。换句话说，async-script 可能在 DOMContentLoaded 触发之前或之后执行，但一定在 load 触发之前执行。

1. **情况3 <script defer src="script.js"></script>(延迟执行)**

defer 属性表示延迟执行引入的 JavaScript，即这段 JavaScript 加载时 HTML 并未停止解析，这两个过程是并行的。整个 document 解析完毕且 defer-script 也加载完成之后（这两件事情的顺序无关），会执行所有由 defer-script 加载的 JavaScript 代码，然后触发 DOMContentLoaded 事件。

defer 与相比普通 script，有两点区别：**载入 JavaScript 文件时不阻塞 HTML 的解析，执行阶段被放到 HTML 标签解析完成之后。 在加载多个JS脚本的时候，async是无顺序的加载，而defer是有顺序的加载。**

#### 2.为什么操作 DOM 慢

把 DOM 和 JavaScript 各自想象成一个岛屿，它们之间用收费桥梁连接。——《高性能 JavaScript》

JS 是很快的，在 JS 中修改 DOM 对象也是很快的。在JS的世界里，一切是简单的、迅速的。但 DOM 操作并非 JS 一个人的独舞，而是两个模块之间的协作。

因为 DOM 是属于渲染引擎中的东西，而 JS 又是 JS 引擎中的东西。当我们用 JS 去操作 DOM 时，本质上是 JS 引擎和渲染引擎之间进行了“跨界交流”。这个“跨界交流”的实现并不简单，它依赖了桥接接口作为“桥梁”（如下图）。

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887863802-3566b4d2-91b5-4a66-bbd9-405db5c16583.webp)

过“桥”要收费——这个开销本身就是不可忽略的。我们每操作一次 DOM（不管是为了修改还是仅仅为了访问其值），都要过一次“桥”。过“桥”的次数一多，就会产生比较明显的性能问题。因此“减少 DOM 操作”的建议，并非空穴来风。

#### 3.你真的了解回流和重绘吗

渲染的流程基本上是这样（如下图黄色的四个步骤）：1.计算CSS样式 2.构建Render Tree 3.Layout – 定位坐标和大小 4.正式开画 

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887863793-f5da73d4-41fa-4234-97a4-d6008d6085d4.webp)

注意：上图流程中有很多连接线，这表示了Javascript动态修改了DOM属性或是CSS属性会导致重新Layout，但有些改变不会重新Layout，就是上图中那些指到天上的箭头，比如修改后的CSS rule没有被匹配到元素。

这里重要要说两个概念，一个是Reflow，另一个是Repaint

- 重绘：当我们对 DOM 的修改导致了样式的变化、却并未影响其几何属性（比如修改了颜色或背景色）时，浏览器不需重新计算元素的几何属性、直接为该元素绘制新的样式（跳过了上图所示的回流环节）。
- 回流：当我们对 DOM 的修改引发了 DOM 几何尺寸的变化（比如修改元素的宽、高或隐藏元素等）时，浏览器需要重新计算元素的几何属性（其他元素的几何属性和位置也会因此受到影响），然后再将计算的结果绘制出来。这个过程就是回流（也叫重排）

我们知道，当网页生成的时候，至少会渲染一次。在用户访问的过程中，还会不断重新渲染。重新渲染会重复回流+重绘或者只有重绘。 **回流必定会发生重绘，重绘不一定会引发回流**。重绘和回流会在我们设置节点样式时频繁出现，同时也会很大程度上影响性能。回流所需的成本比重绘高的多，改变父节点里的子节点很可能会导致父节点的一系列回流。



**1）常见引起回流属性和方法**

任何会改变元素几何信息(元素的位置和尺寸大小)的操作，都会触发回流，

- 添加或者删除可见的DOM元素；
- 元素尺寸改变——边距、填充、边框、宽度和高度
- 内容变化，比如用户在input框中输入文字
- 浏览器窗口尺寸改变——resize事件发生时
- 计算 offsetWidth 和 offsetHeight 属性
- 设置 style 属性的值



**2）常见引起重绘属性和方法**

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646887863800-f7e6b655-7e61-43c0-ac41-12948c0e1841.webp)



**3）如何减少回流、重绘**

- 使用 transform 替代 top
- 使用 visibility 替换 display: none ，因为前者只会引起重绘，后者会引发回流（改变了布局）
- 不要把节点的属性值放在一个循环里当成循环里的变量。

```javascript
for(let i = 0; i < 1000; i++) {
    // 获取 offsetTop 会导致回流，因为需要去获取正确的值
    console.log(document.querySelector('.test').style.offsetTop)
}
```

- 不要使用 table 布局，可能很小的一个小改动会造成整个 table 的重新布局
- 动画实现的速度的选择，动画速度越快，回流次数越多，也可以选择使用 requestAnimationFrame
- CSS 选择符从右往左匹配查找，避免节点层级过多
- 将频繁重绘或者回流的节点设置为图层，图层能够阻止该节点的渲染行为影响别的节点。比如对于 video 标签来说，浏览器会自动将该节点变为图层。

### JS运行机制

刚刚巴拉巴拉讲了这么久，其实只是更好的让大家去理解我们浏览器的工作原理，可能大家会觉得很无聊很枯燥，是这样的，学长我准备这些东西时更无聊，查资料👀  疼哈哈哈。那现在进入最后一个环节：js的运行机制。



大家都知道，js是单线程的，但是虽然如此，但聪明的前辈们还是将任务分成了两种：

- 同步
- 异步



当我们打开网站时，网页的渲染过程就是一大堆同步任务，比如页面骨架和页面元素的渲染。而像加载图片音乐之类占用资源大耗时久的任务，就是异步任务。关于这部分有严格的文字定义，但本文的目的是用最小的学习成本彻底弄懂执行机制，所以我们用导图来说明：

![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646888251233-a9686a3b-e5e1-420d-bc49-df1c849686fd.png)

同步任务都在主线程(这里的主线程就是JS引擎线程)上执行，会形成一个执行栈



主线程之外，事件触发线程管理着一个任务队列，只要异步任务有了运行结果，就在任务队列之中放一个事件回调



一旦执行栈中的所有同步任务执行完毕(也就是JS引擎线程空闲了)，系统就会读取任务队列，将可运行的异步任务(任务队列中的事件回调，只要任务队列中有事件回调，就说明可以执行)添加到执行栈中，开始执行


我们不禁要问了，那怎么知道主线程执行栈为空啊？js引擎存在monitoring process进程，会持续不断的检查主线程执行栈是否为空，一旦为空，就会去Event Queue那里检查是否有等待被调用的函数。



说了这么多文字，不如直接一段代码更直白：

```javascript
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
let setTimeoutCallBack = function() {
  console.log('我是定时器回调');
};
let httpCallback = function() {
  console.log('我是http请求回调');
}

// 同步任务
console.log('我是同步任务1');

// 异步定时任务
setTimeout(setTimeoutCallBack,1000);

// 异步http请求任务
ajax.get('/info',httpCallback);

// 同步任务
console.log('我是同步任务2');
```

上述代码执行过程

JS是按照顺序从上往下依次执行的，可以先理解为这段代码时的执行环境就是主线程，也就是也就是当前执行栈

首先，执行console.log('我是同步任务1')



接着，执行到setTimeout时，会移交给定时器线程，通知定时器线程 1s 后将 setTimeoutCallBack 这个回调交给事件触发线程处理，在 1s 后事件触发线程会收到 setTimeoutCallBack 这个回调并把它加入到事件触发线程所管理的事件队列中等待执行



接着，执行http请求，会移交给异步http请求线程发送网络请求，请求成功后将 httpCallback 这个回调交由事件触发线程处理，事件触发线程收到 httpCallback 这个回调后把它加入到事件触发线程所管理的事件队列中等待执行

再接着执行console.log('我是同步任务2')



至此主线程执行栈中执行完毕，JS引擎线程已经空闲，开始向事件触发线程发起询问，询问事件触发线程的事件队列中是否有需要执行的回调函数，如果有将事件队列中的回调事件加入执行栈中，开始执行回调，如果事件队列中没有回调，JS引擎线程会一直发起询问，直到有为止。

#### 宏任务(macrotask) & 微任务(microtask)



**宏任务(macrotask)**

在ECMAScript中，macrotask也被称为task

我们可以将每次执行栈执行的代码当做是一个宏任务(包括每次从事件队列中获取一个事件回调并放到执行栈中执行)， 每一个宏任务会从头到尾执行完毕，不会执行其他

由于JS引擎线程和GUI渲染线程是互斥的关系，浏览器为了能够使宏任务和DOM任务有序的进行，会在一个宏任务执行结果后，在下一个宏任务执行前，GUI渲染线程开始工作，对页面进行渲染

```javascript
宏任务 -> GUI渲染 -> 宏任务 -> ... 复制代码
```

常见的宏任务

- 主代码块（script）
- setTimeout
- setInterval
- setImmediate ()  -Node
- requestAnimationFrame ()  -浏览器

**微任务(microtask)**

ES6新引入了Promise标准，同时浏览器实现上多了一个microtask微任务概念，在ECMAScript中，microtask也被称为jobs

我们已经知道宏任务结束后，会执行渲染，然后执行下一个宏任务， 而微任务可以理解成在当前宏任务执行后立即执行的任务

当一个宏任务执行完，会在渲染前，将执行期间所产生的所有微任务都执行完

```javascript
宏任务 -> 微任务 -> GUI渲染 -> 宏任务 -> ... 复制代码
```

常见微任务

- process.nextTick ()-Node
- Promise.then()
- catch
- finally
- Object.observe
- MutationObserver



![img](https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/md/1646893593307-de8063df-8c61-499e-95fe-45c278c043af.png)

运行机制已经差不多讲完了，我们来到题练练手吧😝

```javascript
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

第一轮事件循环流程分析如下：

- 整体`script`作为第一个宏任务进入主线程，遇到`console.log`，输出1。
- 遇到`setTimeout`，其回调函数被分发到宏任务Event Queue中。我们暂且记为`setTimeout1`。
- 遇到`process.nextTick()`，其回调函数被分发到微任务Event Queue中。我们记为`process1`。
- 遇到`Promise`，new Promise直接执行输出7。then被分发到微任务Event Queue，我们记为`then1`。
- 又遇到了`setTimeout`，其回调函数被分发到宏任务Event Queue中，我们记为`setTimeout2`。

| 宏任务Event Queue | 微任务Event Queue |
| ----------------- | ----------------- |
| setTimeout1       | process1          |
| setTimeout2       | then1             |

- 上表是第一轮事件循环宏任务结束时各Event Queue的情况，此时已经输出了1和7。
- 我们发现了process1和then1两个微任务。
- 执行process1,输出6。
- 执行then1，输出8。

好了，第一轮事件循环正式结束，这一轮的结果是输出1，7，6，8。那么第二轮时间循环从setTimeout1宏任务开始：

- 首先输出2。接下来遇到了process.nextTick()，同样将其分发到微任务Event Queue中，记为process2。new Promise立即执行输出4，then也分发到微任务Event Queue中，记为then2。

| 宏任务Event Queue | 微任务Event Queue |
| ----------------- | ----------------- |
| setTimeout2       | process2          |
|                   | then2             |

- 第二轮事件循环宏任务结束，我们发现有process2和then2两个微任务可以执行。
- 输出3。
- 输出5。
- 第二轮事件循环结束，第二轮输出2，4，3，5。
- 第三轮事件循环开始，此时只剩setTimeout2了，执行。
- 直接输出9。
- 将process.nextTick()分发到微任务Event Queue中。记为process3。
- 直接执行new Promise，输出11。
- 将then分发到微任务Event Queue中，记为then3。

| 宏任务Event Queue | 微任务Event Queue |
| ----------------- | ----------------- |
|                   | process3          |
|                   | then3             |

- 第三轮事件循环宏任务执行结束，执行两个微任务process3和then3。
- 输出10。
- 输出12。
- 第三轮事件循环结束，第三轮输出9，11，10，12。

整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。 (请注意，node环境下的事件监听依赖libuv与前端环境不完全相同，输出顺序可能会有误差)



## 作业：

### Level1:函数柯里化

```javascript
function sum(a, b, c) {
  return a + b + c;
}

let curriedSum = curry(sum);

alert( curriedSum(1, 2, 3) ); // 6，仍然可以被正常调用
alert( curriedSum(1)(2,3) ); // 6，对第一个参数的柯里化
alert( curriedSum(1)(2)(3) ); // 6，全柯里化

//写一个curry函数来实现此功能
```

### Level2：节流和防抖

```javascript
//分别用节流和防抖去实现一个输入框的优化。
//代码自己实现
```

### Level3：AOP

```javascript
let func = function(){
    console.log(2);
}


func = func.before((a=1) => {
    console.log(a)
}).after((b=3) => {
    console.log(b);
})

func()
//实现func.before()以及func.after()
```

### Level4：

```javascript
//大家手写实现Array.prototype.map
//Array.prototype.filter和Array.prototype.reduce
```