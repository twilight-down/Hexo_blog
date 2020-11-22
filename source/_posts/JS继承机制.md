---
title: JS四大继承方式
date: 2020-11-19 16:45:57
tags: JS四大继承方式
categories: JS
---
# **16、JS四大继承方式**



1、**`原型继承`**

2、**`call继承`**

3、**`寄生组合继承`**

4、**`class实现继承`**



+ JS语言中没有类似后台语言的重载机制

+ JS的重载只是同一个方法，根据传参的不同，实现不同的业务逻辑（调用的都是同一个参数）

+ JS中的继承机制：





##### 1、**`原型继承`**

（B子类   -->   A父类）

子类的`原型`指向父类的一个`实例`

父类上公有私有的属性都能拿到 并且可以加以修改



```html
 <script>
        function A() {
            this.x = 100
        }
        A.prototype.getX = function() {
            console.log(this.x);
        };

        function B() {
             console.log(this.y + 20);
        }

        B.prototype.sum = function() {
            return y + 20
        }   // 丢失了原本在原型链上设置的方法和属性
        B.prototype = new A; // 实现了继承

        B.prototype.getY = function() {
            console.log(this.y);
        };

        let b = new B;
        b.getX()  // 100
        b.getY()  // 200
        b.sum()   // ERROR b.sum is not a function
    </script>
```



**` 原型继承`**的特点： 

​                           1、 并不会把父类的方法克隆一份给子类，而是建立了子类和父类之间的原型链查找机制

​							2、重定向子类原型后，默认丢失了constructor属性（原本在原型上设置的属和方法）							3、子类或子类的实例可以基于原型链，肆意修改父类上的方法 __对父类造成一些不必要的破坏__

​							4、会把父类中私有的方法变成公有的方法被继承（父类中不论是公有还是私有 都变为子类共有																													的内容）	







##### 2、**`call继承`**

把父类当做普通函数去执行，让其执行的时候，方法中的this变为子类的实例

只能拿到私有的方法属性 公有的拿不到



``` js
 function A() {
            this.x = 100
        }
        A.prototype.getX = function() {
            console.log(this.x);
        };

        function B() {
            // call 继承
            A.call(this); //this.x =100  b.x=100
            this.y = 200
        }
        B.prototype.getY = function getY() {
            console.log(this.y);
        }
        let b = new B
        console.log(b);// x: 100
					   // y: 200
		b.getY() // 200
        b.getX() //  报错 
```





**` call继承`**特点：1、只能继承父类中的私有属性（继承的私有属性赋值给了子类实例的私有属性），而且是类似								   于拷贝了一份（浅拷贝 基本数据类型b中改变了a中不受影响 堆内存可能会被修改），而不是								   链式查找

​							 2、因为只是把父类当做普通函数执行，所以父类原型上的公有属性无法被继承过来







##### 3、**`寄生组合继承`**

Call继承+变异版的原型链继承共同完成的

​	    call继承实现：私有到私有

​		原型链实现：公有到公有





```html
<script>
        function A() {
            this.x = 100
        }
        A.prototype.getX = function getX() {
            console.log(this.x);
        }

        function B() {
            A.call(this)//call继承实现：私有到私有
            this.y = 200
        }
        B.prototype = Object.create(A.prototype); //原型链实现：公有到公有
        B.prototype.constructor = B;//prototype重新指向会导致constructor丢失 需要重新指向
        B.prototype.getY = function getY() {
            console.log(this.y);
        }
        let b = new B;
        console.log(b);//  x=100 y=200  getX getY
    </script>
```





##### 4、**`class实现继承`**



```HTML
<script>
        // ES6创建用class
        class A {
            constructor() {
                    this.x = 1000
                }
                // 设置到A.prototype 上的方法
            getX() {
                console.log(this.x);
            }
        }
        class B extends A {
            constructor() {
                // 使用extends继承 写了constructor 就必须写super
                // 可以把 super 理解为A.call(this)
                super();
                this.y = 2000
            }
            getY() {
                console.log(this.y);
            }
        }
        let b = new B
    </script>
```

+ react 创建类组件
+ 自己写插件或者类库的时候