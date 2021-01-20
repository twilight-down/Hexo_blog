---
title: Tapable
date: 2021-01-04 17:32:28
tags: webpack
---
# webPack核心实现Tapable

## 个人理解欢迎指出错误

### 1、SyncHook

#### 1.1.1 SyncHook

EventEmitter ：on注册事件，emit派发事件，并且按照事件注册的顺序去以此执行事件

与 EventEmitter 类似Tapable通过tap注册回调，通过call来触发



同步钩子SyncHook

>  `SyncHook`同步串行，不关心返回值是什么，只会按照注册顺序去以此执行所有注册的事件处理函数
>
>  理解的执行原理： 将所有的注册任务放在一个队列中去执行，每注册一个任务则往队列中push一个task，遇到call去执行时则按顺序依次触发

```js
// 引入Tapable中的同步钩子
const { SyncHook } = require('tapable');
// 创建Hook对象
const Hook = new SyncHook(['name']);
// 注册组件
Hook.tap('hello', (name) => {
    console.log(`hello ${name}`);
});
Hook.tap('hi', (name) => {
    console.log(`hi ${name}`);
});
// 触发事件
Hook.call('andy')
//hello andy
//hi andy

//不在队列当中，不会被执行 
Hook.tap('lovely', (name) => {
    console.log(`lovely ${name}`);
});
```



#### 1.1.2 **SyncBailHook** 

 同步熔断保险钩子 ,return的值不是undefined则停止向下执行



> 与SyncHook的区别为，他关心上一个任务的返回值是什么，只要不为undefined（返回值为null也不可以）那么就会被中断代码的继续向下执行

实现源码：

```js
`if(${result} !== undefined) {\n${onResult(
					result
				)};\n} else {\n${next()}}\n`
```



栗子：

```js
// 引入Tapable中的同步熔断钩子
const { SyncBailHook } = require('tapable')
const Hook = new SyncBailHook(['name'])
Hook.tap('Hello', (name) => {
    console.log(`Hello SyncBailHook ${name} no return`);
});
// return 的是null也会被终结执行，因为 null !==undefined
Hook.tap('TestNull', (name) => {
    console.log(`Hello SyncBailHook ${name} return Null`);
    return undefined
});
Hook.tap('Hi', (name) => {
    console.log(`Hi SyncBailHook ${name}  return is not undefined`);
    return 'stop run task'
});
// 保险熔断 不会被执行
Hook.tap('stop', (name) => {
    console.log(`Bye SyncBailHook ${name}`);
});
Hook.call('andy')
```



#### 1.1.3 SyncWaterfallHook

同步瀑布钩子，上一个函数的返回值会传递给下一个函数



>如果上一个函数的返回值不为undefined那么会作为下一个函数的参数传递进去，回调函数的参数来自上一个非undefined的返回值，如果所有函数的返回值都为undefined那么以第一个传入的参数为其他函数的参数值
>
>例如下面的第一个参数andy，如果第一个参数也没有传递，那么所有函数的参数都为undefined

```js
// 导入引入Tapable中的同步瀑布钩子
const { SyncWaterfallHook } = require('tapable')
// 实例化
const Hook = new SyncWaterfallHook(['name'])

// 注册事件
// 将返回值作为下一个事件的参数传递进去
// 如果上一个返回的参数为undefined则使用上一个非undefined的参数
Hook.tap('Hello', (name) => {
    console.log(`第一次执行 Hello ${name}`);
    name += ' mike';
    return name
});
Hook.tap('Hi', (name) => {
    console.log(`第二次执行 Hello ${name}`);
    name += ' anna';
    return name
});
Hook.tap('Bye', (name) => {
    console.log(`第三次执行 Bye ${name}`);
    return undefined
    // 这里的return undefined可写可不写，完全是为了更加清晰的看到返回值是undefined
});
Hook.tap('test', (name) => {
    console.log(name);
})

// 触发事件
Hook.call('andy')
```





代码实现原理：

```js
	onResult: (i, result, next) => {
				let code = "";
				code += `if(${result} !== undefined) {\n`;
				code += `${this._args[0]} = ${result};\n`;
				code += `}\n`;
				code += next();
				return code;
			},
```



#### 1.1.4 SyncLoopHook

 同步遇到某个不返回undefined的监听函数，就重复执行 



> 同步串行，如果返回值为undefined就会中断循环

```js
// 导入引入Tapable中的同步循环钩子
const { SyncLoopHook } = require('tapable')

// 实例化钩子
const Hook = new SyncLoopHook(['name']);
// 设置计数器
let count = 1;
// 返回值为undefined则跳出循环
Hook.tap('Hello', (name) => {
    console.log(`Hello ${name} 这是${count}次执行`);
    return count++ < 3 ? 'Hi Andy' : undefined
})
Hook.tap('Bye', (name) => {
    console.log(`Bye ${name}`);
})

// 调用钩子
Hook.call('andy')

//Hello andy 这是1次执行
//Hello andy 这是2次执行
//Hello andy 这是3次执行
//Bye andy
```



代码实现原理：

```js
onResult: (i, result, next, doneBreak) => {
				let code = "";
				code += `if(${result} !== undefined) {\n`;
				code += "_loop = true;\n";
				if (!syncOnly) code += "if(_loopAsync) _looper();\n";
				code += doneBreak(true);
				code += `} else {\n`;
				code += next();
				code += `}\n`;
				return code;
			}
```





### 2、AsyncHook

异步钩子AsyncHook

#### 2.1.1 AsyncParallelHook

> 执行任务时，按照EventLoop中任务执行顺序去依次触发事件，有回调函数时会将函数中默认的回调函数覆盖，异步并行



eg1:

```js
//引入Tapable中的异步并行任务AsyncParallelHook
const { AsyncParallelHook } = require('tapable');
//实例化
const Hook = new AsyncParallelHook(['name']);
//计时器
console.time('cost');

// 微任务与宏任务的EventLoop
Hook.tapAsync('Hello', (name, callback) => {
    setTimeout(() => {
        console.log(`Hello ${name}`);
        callback()
    }, 300);
});
//不按照注册顺序执行，而且按照EventLoop的顺序去执行
Hook.tapPromise('Hi', (name) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log(`Hi ${name}`);
            setTimeout(() => {
                console.log('Hi中的定时器');
            }, 10)
            resolve(console.log('resolve被执行'));
        }, 200);
    })
});
Hook.callAsync('andy', () => {
    console.log('异步任务结束');
    console.timeEnd('cost');
})
//Hi andy
//resolve被执行
//Hi中的定时器
//Hello andy
//异步任务结束   
//cost: 305.635ms
//callAsync与promise返回的结果相同
Hook.promise('andy').then(() => {
    console.log('异步任务结束');
    console.timeEnd('cost');
})
```



eg2:

```js
const { AsyncParallelHook } = require('tapable');
const Hook = new AsyncParallelHook(['name']);

console.time('cost');

function callback() {
    console.log('回调函数执行了');
}

// 微任务与宏任务的EventLoop
Hook.tapAsync('Hello', (name, callback) => {
    setTimeout(() => {
        console.log(`Hello ${name}`);
        callback()
    }, 300);
});
Hook.tapPromise('Hi', (name) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log(`Hi ${name}`);
            setTimeout(() => {
                console.log('Hi中的定时器');
            }, 10)
            resolve(console.log('resolve被执行'));
        }, 200);
    })
});

// 有回调函数则不执行后面的回调函数
// 两个钩子会同时执行
Hook.callAsync('andy', callback, () => {
    // 这里的异步任务不会被执行，同样计时器也是
    console.log('异步任务结束');
    console.timeEnd('cost');
})
```



#### 2.1.2 AsyncBailHook





> 熔断指的是promise实例中返回的promise，而tapPromise不允许返回的是undefined，并且只会执行第一个注册事件的promise实例，所以对于每一个事件必定会发生熔断，并且不影响除去Promise实例以外别的任务的执行，只在所有任务执行完之后返回第一个Promise实例（promise的执行时机在所有任务执行完之后）
>
> 特殊情况：如果我们在任务的外面添加了一个定时器，并且第一个执行时间远小于第二个任务的执行时间，那么这时，事件不会在第二个任务执行完毕在执行Promise会在第一个任务执行完成后去执行promise

```js
// 熔断性异步任务
const { AsyncParallelBailHook } = require('tapable');
// 实例化异步任务
const Hook = new AsyncParallelBailHook(['name']);
console.time('cost');
Hook.tapPromise('Hello', (name) => {
    return new Promise((resolve, rejects) => {
        setTimeout(() => {
            console.log(`Hello ${name}`);
            resolve('success');
        }, 200)
    })
})

Hook.tapPromise('Hi', (name) => {
    return new Promise((resolve, rejects) => {
        setTimeout(() => {
            console.log(`Hi  ${name}`);
            rejects('error');
        }, 100)
    })
})

Hook.promise('andy').then(() => {
    console.log('over');
    console.timeEnd('cost');
}, () => {
    console.log('error');
    console.timeEnd('cost');
})

//Hi  andy
//Hello andy
//over
//cost: 205.615ms
```





特殊情况：

```js
// 熔断性异步并行任务
const { AsyncParallelBailHook } = require('tapable');
// 实例化异步任务
const Hook = new AsyncParallelBailHook(['name']);
console.time('cost');
Hook.tapPromise('Hello', (name) => {
    return new Promise((resolve, rejects) => {
        setTimeout(() => {
            console.log(`Hello ${name}`);
            rejects('Wrong');
        }, 100)
    })
})

Hook.tapPromise('Hi', (name) => {
    return new Promise((resolve, rejects) => {
        setTimeout(() => {
            console.log(`Hi  ${name}`);
            resolve('Success');
        }, 200)
    })
})

Hook.promise('andy').then(() => {
    console.log('over');
    console.timeEnd('cost');
    // })
}, () => {
    console.log('error');
    console.timeEnd('cost');
}).catch(() => {
    console.log('Catch');
    console.timeEnd('cost');
})
```



#### 2.2.3 AsyncSeriesHook



> 串行执行，报错则提前执行callback并且终止任务继续执行

eg1、

```js
// 异步任务顺序执行
const { AsyncSeriesHook } = require('tapable');
const Hook = new AsyncSeriesHook(['name']);
console.time('cost : ');


// 与AsyncParallelHook的区别在于Hook执行的顺序
// 修改了任务执行的顺序,类似于async await的感觉
Hook.tapAsync('Hello', (name, callback) => {
    setTimeout(() => {
        console.log(`Hello Series ${name}`);
        callback()
    }, 100);
})

Hook.tapPromise('Hi', (name) => {
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log(`Hi Series ${name}`);
            resolve();
        }, 200);
    })
})

Hook.callAsync('andy', () => {
    console.log('Series was done');
    console.timeEnd('cost : ');
})

// Hook.promise('andy').then(() => {
//     console.log('Series was done');
//     console.timeEnd('cost : ');
// })
```



eg2

```js
// 异步任务顺序执行
const { rejects } = require('assert');
const { AsyncSeriesHook } = require('tapable');
const Hook = new AsyncSeriesHook(['name']);
console.time('cost : ');


// 与AsyncParallelHook的区别在于Hook执行的顺序
// 修改了任务执行的顺序,类似于async await的感觉

//由于提前报错，执行了rejects所以下面的tapAsync不会去执行
Hook.tapPromise('Hi', (name) => {
    return new Promise((resolve, rejects) => {
        setTimeout(() => {
            console.log(`Hi Series ${name}`);
            // resolve();
            rejects('error')
        }, 200);
    })
})

Hook.tapAsync('Hello', (name, callback) => {
    setTimeout(() => {
        console.log(`Hello Series ${name}`);
        callback()
    }, 100);
})



Hook.promise('andy').then(() => {
    console.log('Series was done');
    console.timeEnd('cost : ');
}, () => {
    console.log('Series was error');
    console.timeEnd('cost : ');
})
//Hi Series andy
//Series was error  
//cost : : 209.255ms

```












