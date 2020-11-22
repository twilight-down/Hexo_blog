---
title: Vuex组件传值
date: 2020-11-19 16:45:57
tags: vuex
categories: VUE
---
# **Vuex组件传值**

# tag : vuex

### 1、组件之间的传值方式

![1604656176524](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1604656176524.png)





### 2、Vuex统一管理状态的好处

1、在vuex中集中管理共享数据，易于开发和后期维护

2、能够高效的实现组件之间额数据共享，提高开发效率

3、存储在Vuex中的数据都是响应式的，能够实时保持数据和页面同步	



### 3、Vuex State

State提供唯一数据源，所有共享的数据都要统一放在Store中State中存储

```vue
const  store = new Vuex.Store({	
	state:{count :0}
})
```

组件访问的第一种方式

```vue
this.$store.state.全局数据名称
```





组件访问的第二种方式

 从Vuex中按需导入 mapStete函数

```vue
import {mapState} from 'vuex'
```

通过刚才导入的 mapstate函数 将组件需要的全局数据，映射为当前组件的compute计算属性

```vue
compute:{
 	...mapState(['count'])
}
```



### 4、Mutation 用于变更Store中的数据

1、只能通过mutation变更store中的数据，不可以直接操作Store中的数据

2、可以集中可以集中监控所有数据的变化 便于维护和管理

**add**: commit的作用就是指定调用哪个mutation函数

```vue
mutations:{
add(state){
  //变更状态
  state.count++
}
}
```

```
//触发mutation
methods:{
handel(){
//触发mutations的第一种方式
	this.$store.commit('add')
}
}
```



3、在触发mutation时传递参数

```vue
mutations:{
add(state,step){
  //变更状态
  state.count+step
}
}


//触发mutation
methods:{
handel(){
//触发mutations时携带参数
	this.$store.commit('add',3)
}
}
```







第二种方式

先按需导入mupMutations函数 然后将需要的mutations函数映射为当前组件methods的方法

```
methods:{
    ...mapMutations(['sub','subN']),
    btnHandeler1(){
    //可以直接使用this来调用 而不用使用this.$store.commit('sub')
      this.sub()
    },
    btnHandeler2(){
      this.subN(3)
    }
```





###### 注意mutation中不可以写异步函数



### 5、Action



第一种触发actions的方式

this.$store.dispatch('count')





Action用于处理异步任务

如果通过异步操作变更数据 必须使用Action ，但是在Action中还是要通过触发Mutation方式变更数据

```js
// 定义 Action
const store = new Vuex.Store({
// ...省略其他代码
mutations: {
    add(state) {
        state.count++
       }
     },
actions: {
   addAsync(context) {
      setTimeout(() => {
        context.commit('add')
        }, 1000)
    }
}
})
```



dispatch用于触发对应的action中的函数

```js
// 触发 Action
methods: {
handle() {
// 触发 actions 的第一种方式
this.$store.dispatch('addAsync')
}
}
```





第二种触发actions的方式

```js
import {mapActions} from 'vuex'

methods:{

…mapActions(['addAsync','addNAsync'])

 }
```

这种方式可以直接把click函数绑定位addAsync这种  还省去了在下面（methods）重新定义一个函数





### 6、Getter

getter同于对Store中的数据进行加工处理形成新的数据

1、Getter可以对Store中已有的数据进行加工处理之后形成新的数据，类似Vue中的计算属性

2、Store中的数据变化，Getter中的数据也会跟着一起变化（响应式）



第一种方式

```js
this.$store.getters.名称
```



第二种方式

```js
import { mapGetters} from 'vues'

computed:{

...mapGetters(['showNum'])

}


```





### 7、关于Vuex的总结 

### ![img](https://img2018.cnblogs.com/blog/1545354/201903/1545354-20190313163217143-941299254.png)

###  

