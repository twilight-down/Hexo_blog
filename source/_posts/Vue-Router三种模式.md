---
title: Vue-Router的三种模式
date: 2020-12-01 16:37:56
tags: vue
categories: VUE
---

# Vue-Router(需要后续完善补充)

前言：昨天下午接到了SHEIN的面试，虽然面试官小哥哥人说话很温柔，但是问到我Vue-Router有几种模式还是有点措不及防...



##### Vue-router一共有三种模式

分别为：`Hash`、`history`、 `abstract` 

hash：使用URL的hash值作为路由，支持所有的浏览器

history：依赖 HTML5 History API 和服务器配置

abstract：支持所有的JavaScript环境，如Node.js服务器端.**如果没有发现浏览器的API，路由会强制进入这种模式**







### 1、hash模式

hash模式背后的原理是window.onhashchange事件，我们可以去监听这个事件

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <div>输入#号颜色为字体做相应的改变</div>
    <script type="text/javascript">
        window.onhashchange = function(event) {
            console.log(event.oldURL, event.newURL)
            let hash = location.hash.slice(1);
            document.body.style.color = hash;
        }
    </script>

</body>

</html>
```



Location中包含了hash属性我们可以通过hash属性来给对应的字体设置颜色，但目的其实在于发现hash改变的URL都被浏览器所记录了下来

此时，虽然浏览器没有向服务器发起请求但是页面的状态也和URL关联了起来 （前端路由）



[![DfLBSs.png](https://s3.ax1x.com/2020/12/01/DfLBSs.png)](https://imgchr.com/i/DfLBSs)
[![DfLI61.png](https://s3.ax1x.com/2020/12/01/DfLI61.png)](https://imgchr.com/i/DfLI61)




### 2、History模式

hashchange和History的区别：**hashchange只能改变#后面的部分 而History是不受限制的**

​	**但是在hashchange中可以随意的去修改他#后面的部分（浏览器并不会发送请求）但是History中随意修改     path	如果服务器没有对应的响应或者资源是会响应出404界面的（页面请求的时候会带上整个链接，因此后台需要做相应的处理）**

history API分为两部分：切换和修改



**切换历史状态**

```js
history.go()  //可以自定义前进后退 -1后退一步  3 前进三步
history.back() // 后退
history.forward() //前进
```



**修改历史状态**

 **`history.pushState()`** 

```js
const state = { 'page_id': 1, 'user_id': 5 }
const title = ''
const url = 'hello-world.html'

history.pushState(state, title, url)
```

**`history.replaceState() `**

语法：`history.replaceState(stateObj, title[, url]);`

参数：stateObj title（可以忽略但是应该传递一个空字符串）  url 

 			stateObj ：状态对象是一个JavaScript对象，它与传递给 `replaceState` 方法的历史记录实体相关联. 

​			URL：历史记录实体的URL，新的URL跟当前的URL必须是同源，否则replaceState会抛出异常





[参考](https://developer.mozilla.org/zh-CN/docs/Web/API/History/replaceState)

假设 http://mozilla.org/foo.html 执行下面的 JavaScript 代码:

```js
var stateObj = { foo: "bar" };
history.pushState(stateObj, "", "bar.html");
```

上面这两行的解释可以在 "Example of pushState() method"这个章节找到。然后假设 http://mozilla.org/bar.html 执行下面的 JavaScript 代码:

```js
history.replaceState(stateObj, "", "bar2.html");
```

这会让URL栏显示 http://mozilla.org/bar2.html, 但是不会刷新 `bar2.html` 页面 甚至不会检查bar2.html 是否存在

假设用户跳转到 http://www.microsoft.com, 然后点击返回按钮.这时, URL 栏将会显示 http://mozilla.org/bar2.html 页面. 如果用户此时点击返回按钮, URL栏将会显示 http://mozilla.org/foo.html 页面, 最终绕过了 bar.html 页面.





```js
history.pushState({color:'red'}, 'red', 'red')

window.onpopstate = function(event){
    console.log(event.state)
    if(event.state && event.state.color === 'red'){
        document.body.style.color = 'red';
    }
}

history.back();

history.forward();
```