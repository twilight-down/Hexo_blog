---
title: ES6基础语法
date: 2020-11-19 16:45:57
tags: ES6
categories: JS
---
# **1、ES6基础语法**



## 一、解构

#### 一、数组



##### 1、解构赋值：

主要是针对于数组和对象，

应用场景：一般用于把从服务器上获取的JSON数据进行快速解构，赋值给对应的变量，方便我们快速拿到对应的结果	

```js
let arr = [10, 20, 30, 40];

let [a, b, c, d] = arr; //全部结构赋值
let [m, , n] = arr //只解构出第一个和第三个
let [o, ...p] = arr //拿到第一个 剩余的放在一个数组当中
let [s] = arr.reverse(); //拿到对应的最后一个

console.log(a, b, c, d); // 10 20 30 40
console.log(m, n); //10 30
console.log(o, p); // 10 [ 20, 30, 40 ]
console.log(s); //  40
```



##### 2、赋值默认值：

```js
// 赋值默认值
let arr = [10]
let [a, b = 0] = arr
console.log(a, b);
```



##### 3、交换值：

```js
// a,b 互换值
let a = 10,
    b = 20;
console.log(a, b);
[b, a] = [a, b]
console.log(a, b);
```





#### 二、对象

##### 1、解构赋值

对象解构赋值 创建的变量名需要和属性名保持一致

```js
let obj = {
    id: 1,
    name: 'andy',
    age: 10
}
const { id, name } = obj
console.log(id, name); // 1 andy
```



##### 2、赋值默认值：

```js
let obj = {
    id: 1,
    name: 'andy',
    age: 10
}
const { id, name, sex = 0 } = obj
console.log(id, name, sex); //1 andy 0
```





##### 3、设置别名

```js
let obj = {
    id: 1,
    name: 'andy',
    age: 10
}
let id = 0
const { id: id1, name, sex = 0 } = obj  //由于id已经被声明 可以设置别名
console.log(id1, name, sex);
```



##### 4、补充

null不可以被解构赋值   需要保证解构的不是空对象

undefined 可以给他设置默认值 {} = {}





## 二、扩展运算符

#### 一、...

##### 1、扩展运算符

let [ ... arr]  =[1,2,3,4,5];

##### 2、剩余运算符

函数形参赋值上

```js
let fn = (n, ...m) => {
    console.log(n, m);
}
fn(1, 2, 3, 4, 5) //1 [ 2, 3, 4, 5 ]
```

##### 3、展开运算符

展开数组或者对象中的每一项

```js
// 数组克隆 （展开运算符）
let arr = [1, 2, 3, 4]
let arr2 = [...arr]
console.log(arr2);
```



让 call（参数为一个一个传） 变得和apply一样（参数可以是一个数组）参数都可以是数组

```js
  fn.call(obj, ...arr1) // fn(1,2,3,4)
      //   =>
  fn.apply(obj, arr1)
```

