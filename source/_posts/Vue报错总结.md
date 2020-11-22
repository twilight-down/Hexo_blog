---
title: Vue报错总结
date: 2020-11-19 16:45:57
tags: vue
categories: VUE
---
# **Vue报错总结**


### 1、方法已经定义但是被提示方法没有被定义

原因![1603965660935](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1603965660935.png)

1、方法被定义在methods 外部





### 2.这种问题大多数是因为在变量或者方法前面没有加this导致的![1603976496182](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1603976496182.png)



### 3、对应路由已经设置但是无法显示对应的页面

原因一：导入的路由组件路径错误，无法找到对应文件

原因二：路径前面没有加/



### 4、el-element组件中的Tree 树形控件 

![1604052667050](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1604052667050.png)



default-expand-all文档中默认为false 但是我们希望这个控件一直是展开的所以给他改为true

也就是 default-expand-all = true 这样vue会发出警告 但其实只写属性名可以默认为true 

 解决的办法是，实际上并不用加 true，直接写上这个clearable就行，看似很简单的问题，有时候也会招不到解决办法 



第二种情况是我去设置false属性时的解决方案 可以在前面加上冒号做属性绑定，因为false是一个布尔值不加的话就是一个字符串



### 5.vue打包上线报错“Error: if there’s nested data, rowKey is required.”

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200428102659392.PNG) 

 意思是如果存在嵌套数据，则需要rowKey。Element-UI 2.7.0需要在el-table标签中新增row-key字段，使用树结构时，数据里面需要有id，属性row-key是必填的 



 从版本出发，在index.html中CDN优化打包项目的时候导入的element-ui的版本号进行修改，问题终于得到解决 

版本号要保持一致就没有问题啦





### 5、Vue严格模式 报错

在.eslintrc.js中 将'@vue/standard'注释掉