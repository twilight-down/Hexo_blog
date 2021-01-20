---
title: 对less的总结
date: 2020-12-02 10:17:23
tags: CSS
categories: CSS
---


# Less



###### less出现的背景

css是一门非程序语言、没有变量、函数、scope（作用域）等概念

+ css需要书写大量的看似没有逻辑的代码（CSS冗余）
+ 不方便维护和扩展，不利于复用
+ 没有计算能力



#### 1、less介绍

less是css的扩展语言，也成为了css预处理器，为css加入了程序式语法

在css的基础上，引入了变量，Mixin（混入），运算以及函数等功能，简化了css的编写，降低了css维护的成本



#### 2、less变量

##### 命名规范

@变量名：值；

+ 必须有@作为前缀
+ 不能包含特殊字符
+ 不能以数字开头
+ 大小写敏感



#### 3、less嵌套

子元素的样式直接写在父元素的样式里面

伪类选择器、交集选择器、伪元素选择器 内层选择器前面需要加&

内层选择器的前面如果没有&符号，则它被解析为父选择器的后代

如果有&符号，它就被解析为父元素自身或父元素的伪类	

```less
.header{
  width: 200px;
  height: 200px;
  background-color: aqua;
  a {
    color: sandybrown;
    &:hover{
      color: black;
    }
  }
}
.nav {
  width: 100px;
  height: 100px;
  background-color: slateblue;
  .logo {
    color: springgreen;
  }
  &::before {
    content: "";
  }
}
body {
  :hover{
    background-color: brown;
  }
}
```



通过Easy Less生成的css文件

```css
.header {
    width: 200px;
    height: 200px;
    background-color: aqua;
}

.header a {
    color: sandybrown;
}

.header a:hover {
    color: black;
}

.nav {
    width: 100px;
    height: 100px;
    background-color: slateblue;
}

.nav .logo {
    color: springgreen;
}

.nav::before {
    content: "";
}

body :hover {
    background-color: brown;
}
```





#### 4、less计算属性

**值得注意的是 计算属性中间最好不要有空格要不就都有空格 否则会出现以下在这种情况**



**less**

```less
@border : 5px + 10;  //左右都有空格 正常使用
div {
  width: 200px -50;  // 左边有 右边没有 出现错误
  height: 200px-50;  //  左右都没有 正常使用
  border: @border solid red;
}
```

**css**

```css
div {
  width: 200px -50;  /* 没有生成150px 他的实际长度将会是和盒子或者浏览器宽度一样大 */
  height: 150px;
  border: 15px solid red;
}

```



> 两个数参与运算，如果只有一个数有单位，则最后的结果就以这个单位为准

> 如果两个数都有单位，而且单位不一样，最后的结果以第一个单位为准





### 5、Mixin



#### 1、将class选择器和id选择器混入到其他样式中

```less
// 将class选择器和id选择器混入到其他样式中
.a,.b{
  color: aqua;
}

.mixin-calss{
  .a; // 括号是可选的
  .b()
}
```



#### 2、使用mixin但是又不希望被输出到css文件中

```less
// 使用mixin但是又不希望被输出到css文件中
// 可以在定义mixin的时候在后面加上括号
.other-mixin(){
  background-color: blue;
}

.other-class {
  // 同样的这里的括号也是可选的
  .other-mixin()
}

```





#### 3、Mixin使用选择器

```less
// mixin使用选择器
.hover-mixin(){
  &:hover{
    border: 1px solid #ccc;
  }
}

button {
  .hover-mixin()
}

```





#### 4、 在更复杂的选择器中混入属性，可以叠加多个id或类名



```less
// 在更复杂的选择器中混入属性，可以叠加多个id或类名
#outer{
  .inner{
    color: pink;
  }
}

.complite-mixin{
  #outer > .inner
}
// 下面的写法效果是一样的
// #outer > .inner;
// #outer > .inner();
// #outer .inner;
// #outer .inner();
// #outer.inner;
// #outer.inner();

```







#### 5、命名空间守卫

只有当条件成立时才会去执行

```less
// 命名空间守卫
@mode : nothuge;
#namespace when (@mode = huge){
  .mixin(){/* */}
}

#namespace {
  .mixin() when (@mode = huge) { /* */ }
}

```



#### 6、important关键字

**less**

```less
// !important关键字
.foo{
  background-color: #fff;
  font-size: 20px
}
.unimportant {
  .foo();
}
.important{
  .foo()!important
}
```





**css**

```css
.foo {
  background-color: #fff;
  font-size: 20px;
}
.unimportant {
  background-color: #fff;
  font-size: 20px;
}
.important {
  background-color: #fff !important;
  font-size: 20px !important;
}

```





#### 7、携带参数的mixins



**less**

```less
// 携带参数的混入
// 定义函数
.border-radius(@radius){
  border-radius:@radius
}
// 调用函数
#header {
  .border-radius(4px);
}
// 设置默认值
.font-size(@fontsize:10px){
  font-size: @fontsize;
}
#header{
  .font-size();
  // .font-size;
}
// 不传递参数
.margin(){
 margin:auto;
}
pre{.margin}
```



**对应的css**

```css
#header {
  border-radius: 4px;
}
#header {
  font-size: 10px;
}
pre {
  margin: auto;
}

```





#### 8、mixin可以携带多个参数

参数之间最好用分号隔开，因为逗号有两层含义mixin参数分隔符，或css列表分割符

+ 两个参数，每个参数是逗号分隔的列表: `.name(1, 2, 3; something, else)`.

+ 三个参数，每个参数都是一个数字： `.name(1, 2, 3)`.

+ 使用伪分号创建一个包含逗号分隔css列表的参数的mixin调用: `.name(1, 2, 3;)`.

+ 逗号分隔默认值: `.name(@param1: red, blue;)`.



>less允许定义同名函数，只要参数个数不相同那么调用的就是不同的函数





#### 9、`@arguments`变量

**less**

```less
.box-shadow(@x: 0; @y: 0; @blur: 1px; @color: #000) {
  -webkit-box-shadow: @arguments;
     -moz-box-shadow: @arguments;
          box-shadow: @arguments;
}
.big-block {
  .box-shadow(2px; 5px);
}
```



**css**

```css
.big-block {
  -webkit-box-shadow: 2px 5px 1px #000;
     -moz-box-shadow: 2px 5px 1px #000;
          box-shadow: 2px 5px 1px #000;
}
```





#### 10、作为函数的 mixins

> 从 mixins 中返回变量或 mixins
>  在 mixin 中定义的变量和 mixin 是可见的，可以在调用者的作用域中使用。只有一个例外，如果调用方包含同名变量（包括由另一个 mixin 调用定义的变量），则不复制变量。只有调用方本地作用域中存在的变量才受保护。从父作用域继承的变量将被重写(覆盖)。
>  例如:

**less**

```less
.mixin() {
  @width: 100%;
  @height: 200px;
}

.caller {
  .mixin();
  width: @width;
  height: @height;
}
```



**css**

```css
.caller {
  width: 100%;
  height: 200px;
}
```
