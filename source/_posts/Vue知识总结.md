---
title: Vue知识总结
date: 2020-11-19 16:45:57
tags: vue
categories: VUE
---
# **Vue知识总结**



### 1、Vue中的|

![1604385118624](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1604385118624.png)

这里作为时间过滤器 前面为需要操作的时间 后面是过滤函数

过滤函数可以直接定义在全局

![1604385236841](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1604385236841.png)







#### 1、1在template中 this可以省略不写

### 2、为项目添加进度条

导入nprogress包

```js
// 导入Nprogress对应的css和js  为项目设置加载进度条
import Nprogress from 'nprogress'
import 'nprogress/nprogress.css'
```

在请求开始时显示进度条

在请求结束后隐藏进度条

```js
   // 在 request拦截器中，展示进度条 Nprogress.start()
axios.interceptors.request.use(config => {
        // console.log(config);
        Nprogress.start()
        config.headers.Authorization = window.sessionStorage.getItem('token')
            // 在最后必须return config
        return config;
    })
    // 在response拦截器中隐藏进度条 Nprogress.done()
axios.interceptors.response.use(config => {
    Nprogress.done()
    return config
})
```



### 3、在生产环境中清除开发环境的console.log

在生产环境中安装babel-plugin-transform-remove-console

在babel.config.js文件中写入

```json
'transform-remove-console'
```

但是这样一来在开发环境和生产环境都看不到console.log

所以 我们需要根据开发环境或生产环境动态为其添加配置

```js
// 这是项目发布阶段用到的babel插件
const prodPlugins = []
if (process.env.NODE_ENV === 'production') {
    prodPlugins.push('transform-remove-console')
}


module.exports = {
    presets: [
        '@vue/cli-plugin-babel/preset'
    ],
    plugins: [
        [
            'component',
            {
                libraryName: 'element-ui',
                styleLibraryName: 'theme-chalk'
            }
        ],
        // 发布产品时候的插件数组
        //展开运算符 将数组中的每一项展开放在这里
        ...prodPlugins

    ]
}
```



### 4、生成可视化报告

![1604573584792](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1604573584792.png)





### 5、减少项目体积



// 一定要把这一行代码，写到 静态资源托管之前 否则压缩不生效

![1604575904295](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1604575904295.png)





### 6、不同域名的默认运行端口

http:80

https:443