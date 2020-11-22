---
title: JS中事件对列和事件循环机制
date: 2020-11-19 16:45:57
tags: 事件循环机制
categories: JS
---
# **2、JS中事件对列和事件循环机制**


#### 	1、`js`中的同步编程和异步编程

##### 1、JS本身是单线程的（浏览器分配一个线程供JS自上而下运行）

**（存在一个虚拟的模拟异步操作 同一时间只有一个执行栈  但是能够基于Event Queue / Event Loop 机制把一些方法延迟执行  但是JS始终无法同时两件事 除发送Ajax请求外 因为此时浏览器分配一个线程去执行JS 另外一个线程去发送HTTP事物）**

JS中大部分操作都是同步编程，当前任务完成才会开始下一个编程（任务是逐一完成的）

##### 2、对于某些特殊的需求，也按照异步的编程思维去做

​    [浏览器端]

+ 定时器

+ JS中的事件绑定

+ `AJax`/`Fetch`发送请求

+ `Promise`设计管控异步编程（语法糖 ：`async` /` await`/`generate`）

  [Node端]

  + `progress.nextTick`
  + `setImmediate`
  + FS进行I/O 操作可以是异步

  

  JS中异步操作的运行机制，事件队列Event Queue 和事件循环 Event Loop

  






##### 3、`Event Queue`事件队列

预先把一些不立即执行的方法进行存储，方便后期进行执行

主栈代码执行完了 空闲下来才去事件队列里面执行（**查找是否有需要执行的任务 只要主线程没有空闲下来 不论任务队列的方法是否到了执行的条件都不会去执行**） `先找微任务(如果微任务里面没有) 再找宏任务` 将里面的任务拿到主栈里面去执行（**所有任务都是在执行堆栈里面进行的在事件队列里面只是临时存储这些延迟执行的任务** 在这里面不会执行任务）

微任务 ：`microtask`

微任务按照顺序执行 谁在前面谁先执行

​				1、await是微任务

​				2、Promise（除 new Promise ） =>resolve/reject  await

​				3、Generator 生成器中的yeild	

宏任务：`macrotask`

​		宏任务执行顺序是谁先到达就先执行谁

​		1、定时器是宏任务

​		2、事件绑定

​		3、Ajax/跨域中的异步（HTTP请求）	



##### 4、`Event Loop（循环机制）`

是一套查找机制：事件循环机制

解决 多线程不仅占用多倍的系统资源，也闲置多倍的资源 的问题

 **Event Loop是一个程序结构，用于等待和发送消息和事件** 

 就是在程序中设置两个线程：一个负责程序本身的运行，称为"主线程"；另一个负责主线程与其他进程（主要是各种I/O操作）的通信，被称为"Event Loop线程"（可以译为"消息线程"） 







```js
setTimeout(() => {
    console.log(1);
}, 20);
console.log(2);
setTimeout(() => {
    console.log(3);
}, 10); // 3.10ms的任务先到达时间 
console.log(4);
console.time()
for (let i = 0; i < 90000000; i++) { //1. 此时循环结束，原本放在任务队列的两个宏任务都已经到达了执行条件
    //65ms                       （但此时任务还是无法执行，因为主栈中的任务还没执行完，线程还被占用着）
}; //                                 给定时器一个时间并不是时间到达就执行，可能需要延后执行
console.timeEnd()
console.log(5);
setTimeout(() => {
    console.log(6);
}, 8);
console.log(7);
setTimeout(() => {
    console.log(8);
}, 15);
console.log(9); // 2. 主栈执行完 线程空闲下来了 去event queue中执行为微任务和宏任务

// 2 4 5 7 9 3 1 6 8
```

**注意**：定时器设置0也不会立即执行 浏览器有最小反应时间 例如 5ms

**`注意`**:**可能会认为谁的时间少就执行谁 但其实是谁先到达时间谁先执行**

**补充** ：如果定时器设置时间为负数 那么根据H5的标准时间将被设置为0 又由于浏览器存在最小反应时间

   例如谷歌浏览器是1ms那么这个时间实际就相当于设置了1ms  

**另外** ：如果 定时器在嵌套层级超过5层时，最小延迟变为4ms  但是谷歌还是1ms

​		 实际在浏览器的实现中，不同浏览器规定的嵌套层级和最小延迟都有所不同：在Chrome和Firefox中定时器		 的第5次调用被阻了，Safari是在第6次，Edge是在第3次；Chrome的Blink最小延迟是1ms 

  node 中最小延迟也是1ms





```js
console.log(1);
setTimeout(() => {
    console.log(2);
}, 50);
console.log(3);
setTimeout(() => {
    console.log(4);
    while (1 === 1) {}
}, 0);  // 一直占用主栈 2不会被输出
console.log(5);

// 1 3 5 4 
```







#### 2、JS中Ajax的异步性

异步任务动态获得的元素直接从异步外面操作是不行的  要不将相关操作放在 异步任务内部 要不采取事件委托的方式

核心都是Ajax操作   `axios`也是基于`promise`封装的ajax库

fetch是浏览器内置的发送请求的类（天生就是Promise管控的）



#####  `xhr`状态

 UNSEND:0 创建完默认是0

OPENED:1  已经完成OPEN操作

HEADERS_RECEIVED:2   服务器以及把响应头信息返回了（响应头内容少 响应主体内容多）

 LOADING:3   响应主体正在返回中

 DONE:4    响应主体已经返回

` xhr.open` 第三个参数控同步异步指的是从当前send发送请求 算任务开始 一直到状态任务4才算结束

 同步指的是在此期间所有的任务都不去处理

异步指的是在此期间 其余任务正常处理（会把这个请求的任务放在`EventQueue`中）





```js
let xhr = XMLHttpRequest;
xhr.open('get', 'url', true); // true 代表异步请求  false代表同步 默认值为true异步请求
xhr.onreadystatechange = function() {
    // 监听到状态改变才会触发的时间
    console.log(xhr.readyState); // => 2 3 4 
}
xhr.send();
```



这个输出的还是 2 3 4  绑定任务之前就是1 异步操作 所以 2 3 4 都会被输出 

```js
let xhr = XMLHttpRequest;
xhr.open('get', 'url', true); // true 代表异步请求  false代表同步 默认值为true异步请求
xhr.send(); 
xhr.onreadystatechange = function() {
    // 监听到状态改变才会触发的时间
    console.log(xhr.readyState); // => 2 3 4 
}

```



同步特点 状态不为4啥都干不了

```js
let xhr = XMLHttpRequest;
xhr.open('get', 'url', false); // true 代表同步请求  false代表同步 默认值为true异步请求
xhr.send();  //任务开始 
xhr.onreadystatechange = function() {  //绑定之前状态就是4 触发不了
    // 监听到状态改变才会触发的时间
    console.log(xhr.readyState); // => 没有输出
}

```



事件绑定是异步的 先进行 send（同步任务 send执行时 别的任务不执行   状态为4主栈才会结束） send执行完   执行事件绑定 此时状态码为4 

```js
let xhr = XMLHttpRequest;
xhr.open('get', 'url', false); // false 代表同步请求  false代表同步 默认值为true异步请求
xhr.onreadystatechange = function() {
    // 监听到状态改变才会触发的时间
    console.log(xhr.readyState); // => 4 
}
xhr.send();
```

