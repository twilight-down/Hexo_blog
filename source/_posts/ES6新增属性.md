---
title: object方法
date: 2020-11-19 16:45:57
tags: Object
categories: JS
---
#### 五、object方法

# tag: ES6 Object JS 

1、`object.is([value1],[value2])`

比两个'==' 和 ‘===’ 更加准确

```javascript
console.log(Object.is(+0, -0));  //falise
console.log(Object.is(NaN, NaN));  // true
```



#### 六、Set

特殊结构数组

类似于数组，但成员的值都是唯一的，没有重复的值

 `Set`本身是一个构造函数，用来生成 Set 数据结构。 

可以用来数组去重

```js
let arr = [1, 2, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 5, 5, 5, 2, 2]
console.log(new Set(arr)); //生成的并不是一个数组 而是一个类数组

// 将他转换为数组
arr1 = [...new Set(arr)]
arr2 = Array.from(new Set(arr))
console.log(arr1);
console.log(arr2);
```





#### 七、Map

特殊结构对象

传统对象结构中，键不能是对象

```js
let n = {
    x: 10
}
let obj = {
    id: 1
}
obj[n] = 10
console.log(obj);  //{ id: 1, '[object Object]': 10 }
```





Map支持属性名是对象

```js
let n = {
    x: 10
}
let obj = new Map();
obj.set(n, 10)
console.log(obj); //Map { { x: 10 } => 10 }
```





#### 八、for  of

得到的是一个value  

for in 得到的是一个Key