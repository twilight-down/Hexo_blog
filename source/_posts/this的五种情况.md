---
title: js中This的五种情况
date: 2020-11-19 16:45:57
tags: this
categories: JS
---
# **14、js中This的五种情况**


1、事件绑定

2、普通函数执行

3、构造函数执行

4、箭头函数

5、call/apply/bind





##### 1、事件绑定

This1：给元素的某个事件绑定方法，事件触发，方法执行，此时方法中的This一般指向当前元素本身

DOM0事件绑定

```html
<button id="btn">click</button>
    <script>
        btn.onclick = function anoymous() {
            console.log(this);
        }
        // this -> btn
//------------------------------------------------------
        function anoymous() {
            console.log(this);
        }
        btn.onclick = function() {
                anoymous()
            }
            //  this ->window
    </script>
```



DOM2 事件绑定

```js
 		 // false代表在冒泡阶段执行
        btn.addEventListener('click', function anoymous() {
                console.log(this);
				//this -> btn
            }, false)
//-------------------------------------------------------
            // IE 6 7 8 
        btn.attachEvent('onclick', function anoymous() {
            console.log(this);
            // this -> window
        })
```





.bind 事件 特殊处理

```js
 function fn() {
            console.log(this);
        }
        btn.onclick = fn.bind(window)
// this -> window
```





##### 2、普通函数执行

This2：普通函数执行，它里面的this是谁取决于方法执行前面是否有“点”，有的话，点前面是谁就指向谁，如果没有 this就指向window（严格模式下是undefined）

```js
 function fn() {
            console.log(this);
        }
        let obj = {
            name: 'OBJ',
            fn
        };
      
        fn();
          //this -> window
        obj.fn(); 
          //this -> obj
          obj.hasOwnProperty('name') //this -> obj  true
       	  obj.__proto__.hasOwnProperty('name') //this -> obj.__proto__  false
		  Object.prototype.hasOwnProperty.call(obj, 'name') //this ->obj true
```



###### 2、1对Object的一些补充：

​    1、 hasOwnProperty 用来检测某个属性名是否属于当前对象的**私有属性**

​    2、in 用来检测是否为其属性 **不论是共有的还是私有的**

```js
console.log(obj.hasOwnProperty('name')); //true
console.log(obj.hasOwnProperty('toString')); //false
console.log(('fn') in obj); //true
console.log(('toString') in obj);  //true 原型链上存在这个方法
console.log(Object.prototype.hasOwnProperty.call(obj, 'name')); // true .call方法指回了obj
```

3、匿名函数，自执行函数 this 一般指向是window







##### 3、构造函数执行

This3：构造函数执行 ，函数中的this是当前类的实例

```js
 function Fn() {
            console.log(this);
            // this.xxx =xxx 是给当前实例设置私有属性
        }

        let f = new Fn; //this -> Fn 
```







##### 4、箭头函数

This4：箭头函数中没有本身的this，所用到的this都是其上下文中的this 

（prototype也没有 导致他没有constructor 没有构造器 导致他不能被 new 执行   

   没有arguments实参集合   想用他的实参只能通过...args（剩余运算符）得到）

```js
let obj = {
            fn: () => {
                console.log(this);
            }
        }
        obj.fn() // this -> window
```





##### 5、call/apply/bind

This5:基于call apply  bind 可以改变函数中的this指向（强行改变）

**call apply** 唯一区别：执行函数的时候传递的函数的参数方式区别 call一个一个传  apply 把要传的参数放在数组中一起整体传递

​		func.call([context],10,20)

​		func.apply([context],[10,20])



call:   第一个参数就是改变this的指向，写谁就指向谁（特殊：非严格模式下 null undefined 指向的也是window）

​			严格模式下是undefined

```js
obj = {
            fn() {
                console.log(this);
            }
        }
        obj.fn.call(null) // -> window
        obj.fn.call(undefined) // -> window
        obj.fn.call(12) // -> Number(12)
		obj.fn.call('') // -> String("")
```



**bind**

call/apply都是改变this指向的同时直接把函数执行了，而bind不是立即执行函数，属于预先改变this和传递一些内容  （柯理化函数思想）

场景：在搭配定时器时 不希望他立即执行