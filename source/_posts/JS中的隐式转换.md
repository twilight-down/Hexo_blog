---
title: JS中的隐式转换
date: 2020-12-04 23:03:49
tags: JS
categories: JS
---

# JS中的隐式转换

#### 1、什么是隐式转换?

在JS中，当运算符在运算中，如果两边数据不统一，CPU就无法计算，这时编译器就会自动将两边的数据做一个数据类型转换，转换成一样的数据再进行计算。



### 2、关于隐式转换的规则

##### 1、字符串拼接  "+"

> 'a'+'b'
>
> 1+'a'



> 但是字符串拼接有几个需要注意的地方
>
> 如下

```js
console.log(1 + 'a'); // 1a  字符串拼接
console.log(1 + "true"); //1true  字符串拼接
console.log(1 + true); //2  算术运算
console.log(1 + undefined); //NaN  算术运算
console.log(1 + null); // 1   算术运算
```

> 分析
>
> 此处的+分为两种情况  一种是隐式转换 **字符串连接的+**另一种是**算术运算符+**

 

##### 2、关系运算符  ">","<",">=","<=","==","!=","===","!==="

> a>b
>
> 2>10
>
> `NaN` ==`NaN`



> 同样 关系运算符也有需要注意的地方
>
> 如下

```js
// 只有一边是字符串时会将该数据按照Numbe(xx)去进行转换
console.log("2" > 10);  //false
```



```js
// 当关系运算符两边都是字符串时会都转换为字符串然后进行比较
// 但是转换规则有所不同 并不是按照Number(xx)来转换的
// 而且转换为字符串的unicode去转换
// 在转换时会调用.chartCodeAt
// 这个方法会将第一个字符进行转换
console.log("2" > "10");  //true
console.log("2".charCodeAt());  //50
console.log("10".charCodeAt()); // 49
```



```js
// 多个字符的情况 
// 同样也是按照Unicode的规则去转换
// 多个字符进行比较的时候是从左往右依次进行比较的
// 如下先比较 a和 b 不相等则直接得出结果
// 如果值相同则比较下一个 直到得出结果为止
console.log("abc" > "b");  //false
console.log("b" > "abc");  // true
console.log("abc" > "aaf");// true

```



```js
// 几个特殊情况
console.log(undefined == undefined);// true
console.log(null == null);// true
console.log(undefined == null);// true
console.log(NaN == NaN);  //false
```





##### 3、复杂数据类型

经典面试题  前面博客有写

```js
a==1&&a==2&&a==3
```



##### 4、逻辑非隐式转换与关系运算符隐式转换

> 数组`toString()`方法会转换成空字符串，对象`toString()`会得到字符串`'[object Object]'`


> 关系运算符会将其他数据类型转换为数字进行比较
>
> 逻辑非会将其他数据通过Boolean()转换为布尔型
>
> 可以转换为false的情况：+0（0），-0，NaN，undefined，null，""(空字符串)


```js
// 原理[].valueOf().toString() 得到空字符串
// Number('')==0
console.log('' == []); //true
console.log('' == 0); //true
console.log([] == 0); //true

```



```js
// 其实本质上是![]的结果与0进行比较
// 逻辑非的优先级高于关系运算符
// 空数组转换布尔值得到true取反得到false false==0
console.log(![] == 0); //true
console.log([] == ![]); //true
```



```js
// 引用数据类型存储在堆中，栈中存储的是地址，两个空数组的地址不同
console.log([] == []); //false
```



```js
// {}.valueOf.toString()得到的是[object Object]
// Number('[object Object]')得到的结果是NaN
// !{}==false
// Number(false) == 0
// Number('[object Object]') == Number(false)   false
console.log({} == !{}); //false
```



```js
// 引用数据类型存储在堆中，栈中存储的是地址，两个空对象的地址不同
console.log({} == {}); //false
```





##### 5、值得一提的是

`parseInt`在进行转换时第一个参数会先转换成字符串**（隐式转换）**，第二个参数可以指定转换的类型，如果没有指定默认是十进制数据，然后再将数据转换成数字返回

```js
parseInt(-15.1, 10);  //-15
parseInt("15,123", 10); //15
parseInt(15.99, 10);  //15
```






##### 补充

```js
0+{} //0[object Object]
{}+0 //0
0-{} //NaN valueOf toString Number
{}-0 // -0
0 == [] //true
```



> 理解：
>
> 我们应该以为 `0+{}` 和`{}+0`应该得到的结果是相同的 
>
> 但是在运算过程中会调用隐式转换，`valueOf` 和 `toString()`这两个方法,在这个过程中只要解析出字符串就会进行字符串拼接。
>
> 但是在我们解析{}时{}也会被看做是一个空block
>
> {}被解析为空block 加号就变成了正号也就是对一个空数组进行正号运算，也就是将空数组转换为数字，也就是0

