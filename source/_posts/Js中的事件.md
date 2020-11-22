---
title: 事件对象和事件传播机制
date: 2020-11-19 16:45:57
tags: 阻止默认行为 
categories: JS
---
# **18、事件对象和事件传播机制 需要补充**



1、给元素的事件行为绑定方法，当事件行为触发方法会被执行，与此同时还会把当前操作的相关信息传递给这个函数  =>事件对象



preventDefault阻止默认行为的方法

不兼容 用 ev.returnValue = false

stopPropagation 阻止冒泡传播

不兼容浏览器 ev.cancelBubble = true 



# **19、阻止默认行为 需要补充**



preventDefault阻止默认行为的方法

stopPropagation 阻止冒泡传播



1、阻止a标签的默认行为 （点击标签 先触发click事件再去执行herf跳转）

- herf= ":;" / herf = "javascript:"

- 在点击事件中直接return false

- ```html
  <body>
      <!-- a标签的默认行为 页面跳转 -->
      <a href="http://www.twilight-down.cn" target="blank" id="link">asd </a>
      <!-- 锚点定位 -->
      <script>
          link.onclick = function(ev) {
              return false
          }
      </script>
  </body>
  ```

  ev.preventDefailt



# **20、事件的传播机制**

**`冒泡传播`**：触发当前元素的某一个事件行为 不仅他的行为触发了而且他所有的祖先元素（一直到window ）相关				    的事件行为都会被依次触发（从内到外）



事件传播机制：

+ 捕获阶段：从最外层向最里层事件源依次进行查找（目的：为冒泡阶段事先计算好传播层级路径）
+ 目标阶段：当前元素的相关事件行为触发
+ 冒泡传播：触发当前元素的某一个事件行为 不仅他的行为触发了而且他所有的祖先元素（一直到window ）     相关的事件行为都会被依次触发（从内到外）



```html 
<body>
    <div id="outer">
        <div id="inner">
            <div id="center"></div>
        </div>
    </div>

</body>
<script>
    // window.onclick = function() {
    //     console.log('Window');
    // }
    document.onclick = function(e) {
        e.stopPropagation();
        console.log('Body');
    }
    outer.onclick = function(e) {
        // e.stopPropagation();
        console.log('Outer');
    }
    inner.onclick = function(e) {
        e.stopPropagation();
        console.log('Inner');
    }
    center.onclick = function(e) {
        e.stopPropagation();
        console.log('Center');
    }
    outer.addEventListener('click', function() {
        //false 代表在捕获阶段执行
        // true代表在冒泡阶段执行
    }, false)
</script>
```





**DOM0事件**： 绑定的方法只能在目标阶段和冒泡阶段执行

**DOM2事件**： 可以控制其执行的时机

​	

```js
  outer.addEventListener('click', function() {
        //true 代表在捕获阶段执行 基本不用TRUE
        // false 代表在冒泡阶段执行
    }, false) // 默认就是False
```





# **21、MouseOver 与MouseEnter** 的区别

**`MouseOver`**本身不是进入，而是看鼠标在谁上面，存在事件传播（冒泡）。 在父盒子里面的子盒子并不代表在父盒子身上（over和out只看鼠标在谁上面）

 





MouseEnter  MouseLeave 默认阻止了事件传播不存在事件的传播（冒泡传播）

从父盒子进入子盒子，从子盒子进入父盒子都不会触发**父盒子**的enter 和leave（他认为你还在我的容器当中）





# **22、事件委托**

**`事件委托`**：利用事件的冒泡机制完成的（当前元素的某个事件行为触发，那么其祖先元素的相关事件行为都会被触发）





案例：假设一个大容器中有N个小容器，N个子元素被点击时都要做点事情

方案一：给每个子元素都绑定一个方法，点击谁触发谁

方案二：给容器的点击行为添加绑定方法，这样不管我们点击那个元素，容器的点击行为也一定会触发，此时我们在容器中根据事件源的不同，处理不同的事情即可  ----> 事件委托（性能更好）