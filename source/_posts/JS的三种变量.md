---
title: var let const
date: 2020-11-19 16:45:57
tags: 变量提升
categories: JS
---
# 二、var let const



### 1、变量提升

1、在代码执行之前会把所有带 var 或者function 关键字的声明或者定义（var只会提前声明 function提前声明加定义）（用let声明的变量不存在这个特性）

2、全局变量对象用var声明的变量也会给window对象增加一个对应的属性（仅限于全局存在这个特性）（node环境下不存在这个点  node 的全局是Global）

```
var x=12
console.log(window.x);//12
```

```
let x=2
console.log(window.x)//undefined
```



3、补充 变量提升的顺序

  arguments  >  function >  var 

 **函数提升优先级高于变量提升，且不会被同名变量声明时覆盖，但是会被同名变量赋值后覆盖** 



我的理解：  ‘hi’被传入进来以后 arguments接受实参 然后进行函数提升和变量提升 由于函数提升优先于变量提升

所以此时arg被function所覆盖  再进行变量提升 但并没有赋值  所以此时arg是function 并且被打印输出 

执行到第三行 arg 被重新赋值所以最后输出hello 

```js
function test(arg) {
    console.log(arg);
    var arg = 'hello';

    function arg() {
        console.log('hello world')
    }
    console.log(arg);
}
test('hi');   //ƒ arg() {
       		  //	 console.log('hello world')
   			  //	 }
			  //    hello
```



### 2、let不允许重复赋值

编译阶段（编译器）

词法解析=>AST抽象语法树（给浏览器引擎去运行的）

引擎执行阶段



在词法解析阶段会报错

```js
console.log('ok')//这段代码不会执行
let x=1;
let x=2;
console.log(x);//报错 而且是在编译阶段
```



在for循环中设置定时器 并希望他按顺序输出对应的值

```js
for( var i=0;i<5;i++){
    setTimeout(_=>{
        console.log(i)
    },10);
}// 55555
```



利用了闭包的机制 setTimeout=>window.setTimeout

   **_  **    是一个变量名也可以写成（）

缺点是  会形成五个闭包 造成内存泄漏 而且在外面也可以拿到  i

```js
for( var i=0;i<5;i++){
    ~function(i){
    setTimeout(_=>{
        console.log(i)
    },10);
    }(i)
}//0 1 2 3 4 	
/*---------------------------*/
for( var i=0;i<5;i++){
    ~function(i){
    setTimeout(()=>{
        console.log(i)
    },10);
    }(i)
}//0 1 2 3 4 	
```



另一种方法      let 具有块及作用域（var没有      其实还是闭包的机制）

优点是 let还是一个块及作用域 不会造成内存泄漏 在外面访问不到  i

```
for( let i=0;i<5;i++){
    setTimeout(_=>{
        console.log(i)
    },10);
}//0 1 2 3 4 
```



### 3、js暂时性死区

执行不定义 暂时性死区 

```js
consloe.log(typeof a )//undefine
```



执行后定义报错

```js
console.log(a);//报错
let a;
```





### 4、const

const在定义时就必须赋值

let创建的变量可以修改指针的指向 也就是给他重新赋值

而 const声明的变量是不允许改变指针的指向的（也就是不允许重新赋值）