---
title: 在for循环中利用闭包实现定时器
date: 2020-11-19 16:45:57
tags: 闭包 
categories: JS
---

# **13、在for循环中利用闭包实现定时器**





关键点在于 让定时器内部变量与外界的 i 构成强引用形成闭包

```js
for (var i = 0; i < 5; i++) {
    fun(i)
} //0 1 2 3 4
function fun(i) {
    setTimeout(function() {
        console.log(i);
    }, 10)
}


// --------------

for (i = 0; i < 5; i++) {
    (function(i) {
        setTimeout(function() { //这里不可以写参数 让他构成闭包
            console.log(i);
        }, 10)
    })(i)
} //0 1 2 3 4
```



