---
title: 数据类型
date: 2020-11-19 16:45:57
tags: 数据类型
categories: JS
---
# **一、数据类型**



### 1、基本数据类型（值类型）

 nnusb -- s
纳尼USB -- 是 
null number string undefined boolean  -- es6新增 symbol 

number string  Boolean null undefined symbol

### 2、引用数据类型

object ：{} []  /^$/  new Data()日期对象 Math  实例对象 function

### 3、ES6新增

symbol创建唯一值



typeof检测数据类型 返回字符串

inNAN检测数据类型 返回布尔值

### 4、可能出现NAN的情况

parseInt/Float Number() 数学运算

NAN与任何值都不相等 NAN===NAN也是假

number+undefined=NAN



### 5、object键值对的键

也就是对象的属性名 

键可以理解为是一个字符串 但其实他是基本数据类型 

对象名不能是引用数据类型     引用数据类型作为键（属性名）会被转换为字符串

```javascript
let a ={
    x:200
};
let b= {
    y:200
};
let obj={};
obj[a]='aaa'
obj[b]='ccc'


{ '[object Object]': 'ccc' }
```

我们以为obj[a]应该是aaa

但是 结果是 { '[object Object]': 'ccc' }

<!---->

对象toString转换成的都是    [object Object ]

```javascript
({y:200}).toString()
```



### 6、立即执行函数

立即执行函数前面加~可以接受返回值

```
let a= ~function(){}() //可以用变量接受
(function(){})() //不能用变量接受
```







### 7、逻辑与 逻辑非

A||B     A为真返回A 否则B	

A&&B    A为真返回B 否则 A

&&优先级大于||





### 8、数组的slice（）

x=x.slice(0);

两个x值相同但是地址不相同实现了浅克隆（深拷贝）



**slice和concat这两个方法，仅适用于对不包含引用对象的一维数组的深拷贝**

一维数组深拷贝slice和concat方法

\1. slice()

slice()语法：arrayObj.slice(start,[end])；

start：必需。规定从何处开始选取。如果是负数，那么它规定从数组尾部开始算起的位置。也就是说，-1指最后一个元素，-2 指倒数第二个元素，以此类推。

end:可选。规定从何处结束选取。该参数是数组片断结束处的数组下标。如果没有指定该参数，那么切分的数组包含从 start 到数组结束的所有元素。如果这个参数是负数，那么它规定的是从数组尾部开始算起的元素。

返回值:返回一个新的数组，包含从start 到 end （不包括该元素）的arrayObject 中的元素（如果 end 未被规定，那么slice() 方法会选取从 start 到数组结尾的所有元素）。

![img](https://img-blog.csdn.net/20180516204750775)

2.concat()方法

  concat() 方法用于连接两个或多个数组。该方法不会改变现有的数组，而仅仅会返回被连接数组的一个副本。

语法：arrayObject.concat(arrayX,arrayX,......,arrayX)

说明：返回一个新的数组。该数组是通过把所有 arrayX 参数添加到 arrayObject 中生成的。如果要进行 concat() 操作的参数是数组，那么添加的是数组中的元素，而不是数组。

![img](https://img-blog.csdn.net/20180516204803749)





浅拷贝： arr1=arr2地址相同 改变arr1的值arr2也会跟着去改变









**三、slice,concat方法的局限性**

测试例子1

```
var arr1 = [{"name":"weifeng"},{"name":"boy"}];//原数组
var arr2 = [].concat(arr1);//拷贝数组
arr1[1].name="girl";
console.log(arr1);// [{"name":"weifeng"},{"name":"girl"}]
console.log(arr2);//[{"name":"weifeng"},{"name":"girl"}]
```

测试结果：

![img](https://images2015.cnblogs.com/blog/1118847/201703/1118847-20170308141956766-1597438894.png)

 

测试例子2

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
var a1=[["1","2","3"],"2","3"],a2;
a2=a1.slice(0);
a1[0][0]=0; //改变a1第一个元素中的第一个元素
console.log(a2[0][0]);  //影响到了a2

var b1=[["1","2","3"],"2","3"],b2;
b2=b1.slice(0);
b1[0][0]=0; //改变a1第一个元素中的第一个元素
console.log(b2[0][0]);  //影响到了a2
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

测试结果：

![img](https://images2015.cnblogs.com/blog/1118847/201703/1118847-20170308142214063-1416094394.png)

 

从上面两个例子可以看出，由于数组内部属性值为引用对象，因此使用slice和concat对对象数组的拷贝，整个拷贝还是浅拷贝，拷贝之后数组各个值的指针还是指向相同的存储地址。

因此，slice和concat这两个方法，仅适用于对不包含引用对象的一维数组的深拷贝





### 9、谷歌浏览器的垃圾回收机制

浏览器会在空闲的时候把所有不占的堆内存，进行释放和销毁



### 10、IE浏览器机制

当前堆被占用一次则数字加一 以此类推 取消占用减一 0销毁







### 11、自执行函数的this指向

指向Window

 匿名函数的this一般都是指向window的 





### 12、this的执行主体

**第一种**：函数执行 前面是否有点   点的前面是谁 this就指向谁没有点指向的就是window(严格模式下是undefined)

​				自执行函数的this一般都是window  匿名函数的this一般都是指向window的 

**第二种**:给元素的事件行为绑定方法（DOM0 /DOM1），事件触发，方法会执行，此时方法中的this一般都是元素			本身  （特殊情况 IE8及以下，基于attachEvent完成DOM2事件绑定，this的指向不是 很准确）







### 13、DOM0，DOM1，DOM2，DOM3级事件与event对象

**DOM事件级别**

| DOM 0 | element.onclick = function(){}                       |
| ----- | ---------------------------------------------------- |
| DOM 2 | element.addEventListener(‘click’,function(){},false} |
| DOM 3 | element.addEventListener(‘keyup’,function(){},false} |

 

 

 

 

因为DOM 1一般只有设计规范没有具体实现,所以一般跳过. 其中 IE 浏览器的 addEventListener函数对就是 attachEvent函数

 

EVENT对象

| 函数                     | 功能                 |
| :----------------------- | :------------------- |
| preventDefault           | 阻止默认行为         |
| stopPropagation          | 停止事件传递         |
| stopImmediatePropagation | 阻止其它绑定事件执行 |
| currentTarget            | 当前发生事件的元素   |
| target                   | 触发事件的节点       |



### 14、node 和 浏览器的全局对象

node的全局对象是global 浏览器的全局对象是window

