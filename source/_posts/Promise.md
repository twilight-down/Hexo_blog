---
title: Promise
date: 2020-11-19 16:45:57
tags: promise
categories: JS
---
#### Promise**



回调地狱：上一个回调函数中继续做事情，而且继续回调   异步请求 不容易维护

Promise的诞生就是为了解决异步请求中的回调地狱问题：他是一种设计模式

ES6中提供了一个JS内置的Promise，来实现这种设计模式





##### 1、Promise语法

promise本身并不是异步的 promise是用来管理异步编程的

new Promise 的时候会立即执行executor函数（只不过我们一般会在executor中处理一个异步函数）

```js
//  new Promise([executor])
// [executor] 执行函数是必传的
let p1 = new Promise(() => {
    console.log(1);
});
console.log(2); // 1 2
```







```js
let p2 = new Promise(() => {
    setTimeout(() => {
        console.log(1);
    }, 100)
    console.log(2);
})
console.log(3);  //2 3 1
```



##### 2、Promise的三个状态[[`PromiseStatus`]]

###### 1、pending 初始状态

###### 2、fulfilled 表示操作成功（`resolved`）

###### 3、rejected 代表当前操作失败



##### 3、Promise的value值[[`PromiseValue`]]

用来记录成功的结果或者失败的原因

一般在异步操作结束以后，执行**resolve**或**reject**函数其中的一个，都可以修改Promise的`PromiseStatus`和`PromiseValue`



谁先处理的就以谁为主

```js
let p1 = new Promise((resolve, reject) => {
    setTimeout(_ => {
        //一旦状态被改变 执行resolve 和reject就没有用了
        resolve('ok');
        reject('no')
    }, 100)
});
//Promise {<fulfilled>: "ok"}
//__proto__: Promise
//[[PromiseState]]: "fulfilled"
//[[PromiseResult]]: "ok"
```



##### 4、then：设置成功成功或者失败后的处理方法

`Promise.prototype.then([resolvedFn],[rejectdFn])`



new Promise的时候先执行executor函数，在这里开启一个异步任务

（此时将定时器放在Event Queue任务队列中），继续执行

p1.then基于Then方法，存储起来两个函数（此时还没有执行） 等executor中执行完毕后，基于resolve和reject控制的Promise状态 ， 从而再继续执行then存储的某一个方法

```js
let p1 = new Promise((resolve, reject) => {
    setTimeout(_ => {
        if (Math.random() < 0.5) {
            reject('no')
            return
        }
        resolve('ok')
    }, 100)
});

p1.then(result => {
    console.log(result); // 成功时输出
}, reason => {
    console.log(reason);  //失败是输出
})

```



**补充**：**resolve和reject的执行，不论是否放在一个异步操作中，都需要等待then先执行完，把方法存储好，才会在then方法中执行对应的方法 所以此处是一个异步操作 而且是微任务操作（并不准确可以理解为同步任务）**

```js
let p2 = new Promise((resolve, reject) => {
    console.log(1);
    resolve(100);
    console.log(2);
})
p2.then(result => {
    console.log('成功' + result);
}, reason => {
    console.log('失败' + reason);
})
console.log(3);
// 1 2 3 成功100
```





Then 方法结束以后都会返回一个新的Promise实例

[[`PromiseStatus`]]：‘pending’

[[`PromiseValue`]]： undefined







##### 5、写法代替

创建一个状态为成功/失败的Promise实例

```js
Promise.resolve（100）

Promise.reject（0）
//相互代替
new Promise((resolve,reject)=>{
    resolve(100)
    //reject(0)
})
```







##### 6、then





###### 1、状态改变

p1这个实例new promise出来的实例，成功或者失败，取决于executor函数执行的时候，执行的是resolve还是reject决定，在或者executor函数执行发生异常错误也会把实例状态改为失败

p2 p3这种每一次执行then返回的新实例的状态，由then中存储的方法执行的结果来决定最后的状态

（上一个then某个方法执行的结果，决定下一个then中哪一个方法会被执行）

**不论哪个方法执行，凡是抛出异常，都会把实例的方法状态改为失败**

如果方法返回一个**新的Promise实例**，返回实例的结果 成功还是失败 也**决定了当前实例是成功还是失败**

**其余情况都是让实例成功的情况**





```js
let p1 = new Promise((resolve, reject) => {
    resolve(100)
})
let p2 = p1.then(result => {
    console.log('成功' + result);
    return 200;
}, reason => {
    console.log('失败' + reason);
    return -10
});
let p3 = p2.then(result => {
    console.log('成功' + result);

}, reason => {
    console.log('失败' + reason);

});
//成功100
//成功200
```







###### 2、前一个状态报错

resolve执行报错 `not define` 所以第一个`.then`状态是失败 执行reason  他的执行是成功的 所以第二个`.then`执行成功的 由于上一个没有返回值 所以result 是undefined

```js
new Promise(resolve => {
    resolve(a);

}).then(result => {
    console.log('成功' + result);
    return result * 10
}, reason => {
    console.log('失败' + reason); //失败ReferenceError: a is not defined
}).then(result => {
    console.log('成功' + result); //成功undefined
}, reason => {
    console.log('失败' + reason);
})
```

**补充：**这里的a如果是一个字符串 那么输出将会是 成功a 成功NaN







###### 3、返回值是一个新的Promise实例

返回的是一个新的Promise实例 所以下一个结果根据返回的Promise实例更改状态

```js
Promise.resolve(10).then(result => {
    console.log('成功' + result);    //成功10
    return Promise.reject(result * 10)
}, reason => {
    console.log('失败' + reason);
}).then(result => {
    console.log('成功' + result);
}, reason => {
    console.log('失败' + reason);   //失败100
})
```

  





###### 4、只定义一个方法

如果第一个`.then `没有定义失败的就往下顺延

then中可以只写一个或者不写函数

要执行成功或者失败的方法，如果此方法没有定义那就顺延到下一个then里面的对应函数

而正常情况 没有成功只写失败 写的是`catch`	

`Promise.prototype.catch(fn) ` ===> `.then(null,fn)`

```js
Promise.reject(10).then(result => {
    console.log('成功' + result);
    return result * 10
}).then(null, reason => {
    console.log('失败' + reason);
})
```







###### 5、catch方法



```js
Promise.resolve(10).then(result => {
    console.log(a); //报错
}).catch(reason => {
    console.log('失败' + reason);   //失败ReferenceError: a is not defined
})
```







###### 6、Promise.all(arr)

返回的结果是一个Promise实例（all实例）

要求ARR数组中的每一项都是一个promise实例，

promise.all是等待所有数组中的实例状态都为成功才会让all实例状态为成功 

Value是一个集合 存储着arr中每一个实例返回的结果，凡是arr中有一个实例状态为失败All 实例的状态也是失败





返回的结果是按ARR中编写的顺序组合在一起的

```js
let p1 = Promise.resolve(1);
let p2 = new Promise(resolve => {
    setTimeout(_ => {
        resolve(2)
    }, 1000)
});
let p3 = Promise.reject(3)
Promise.all([p1, p2, p3]).then(result => {
    console.log('成功' + result);
}).catch(reason => {
    console.log('失败' + reason); //  失败 3
})


Promise.all([p1, p2]).then(result => {
    console.log('成功' + result); //成功1,2
}).catch(reason => {
    console.log('失败' + reason);
})
```





###### 7、Promise.race(arr)

和All不同的是 race是赛跑 不管Arr中哪个先处理完，先处理完的结果作为Race的实例的结果



如果同一程序中 同时存在All和Race 那么 大部分情况下Race会先执行完

```js
Promise.race([p1, p2]).then(result => {
    console.log('成功' + result);
}).catch(reason => {
    console.log('失败' + reason);
}) //成功1
```







###### 补充 对race all 执行顺序的总结

一般来说race会快于all 而且all中如果有异常抛出会提前终止任务 会比正常全部执行完更快

所以 我们可以得出结论 all中 **任务是同步任务先执行/异步任务后执行**  如果全是异步任务那么应该看那个错误先被执行





```js
let p1 = Promise.resolve(1);
let p2 = new Promise(resolve => {
    setTimeout(_ => {
        resolve(2)
    }, 1000)
});
let p3 = Promise.reject(3)

Promise.all([p1, p2, p3]).then(result => {
    console.log('成功' + result);
}).catch(reason => {
    console.log('失败' + reason); //  失败 3
})


Promise.all([p1, p2]).then(result => {
    console.log('成功' + result); //成功1,2
}).catch(reason => {
    console.log('失败' + reason);
})

Promise.race([p1, p2]).then(result => {
    console.log('成功' + result);
}).catch(reason => {
    console.log('失败' + reason);
})

Promise.race([p1, p3]).then(result => {
    console.log('成功1 ' + result);
}).catch(reason => {
    console.log('失败' + reason);
})

//成功1
//成功1 1
//失败3
//成功1,2

```



**补充**

```js
let p4 = Promise.reject(setTimeout(_ => {
    console.log(600);
}, 1000))
Promise.all([p1, p2, p4]).then(result => {
    console.log('成功' + result);
}).catch(reason => {
    console.log('失败' + reason); //  失败 3
})
//这里的 600 也会输出 并且是在所有任务都执行完毕之后再输出
```



###### 8、ES7操作语法糖 async await

加`async` 返回的就是一个`Promise`实例

`Async`是让一个普通函数返回的结果变为一个status = resolve 并且value = return结构的Promise实例





`async`最主要的作用是配合await

函数中使用`await`必须使用`async`修饰



```js
let p1 = Promise.resolve(100);
async function fn() {
    console.log(1);
    // await 会等待当前promise返回的结果，只有当返回结果是resolvd情况 才会赋值给result
    let result = await p1; //代码执行到此（先把此行执行），构件一个异步微任务 等待Promise结果并且在							 Await下面的代码也会被列到任务队列中
    console.log(result);

}
fn();
console.log(2);  
//1 2 100
```

**补充**      **await并不是同步 而是异步 属于微任务**

 await表达式会**暂停整个`async`函数的执行进程并出让其控制权**，只有当其等待的基于promise的异步操作被兑现或被拒绝之后才会恢复进程 





如果同时有两个await

```js
let p1 = Promise.resolve(100);
let p2 = new Promise(resolve => {
    setTimeout(_ => {
        resolve(200)
    }, 1000)
});
async function fn() {
    console.log(1);
    // await 会等待当前promise返回的结果，只有当返回结果是resolvd情况 才会赋值给result
    let result2 = await p2;  //这行代码以下的都在 等待 执行到等待1000s后 在继续向下 执行
    console.log(result2);	//此时 p1不会执行 必须等待 p2执行完之后再回继续执行

    let result1 = await p1;
    console.log(result1);

}
fn();
console.log(2);

// 1 2 200 100
```





如果 Promise是失败状态则 await不会接受其返回的结果 await下面的代码也不会继续执行

await只能处理Promise是成功的状态的时候

```js
let p3 = Promise.reject(3)
async function fn() {
    let reason = await p3;
    console.log(reason);
    console.log(1);
} //没有输出
```







###### 9、字节跳动练习题





```js
async function async1() {
    console.log('async1 start');  // 4、
    await async2();        // 5、先执行本行代码 但是下面的代码需要等待返回正确的Promise结果后执行																		（异步 -》 微任务）
    console.log('async1 end'); //10、异步任务 微任务开始执行
}
async function async2() {
    console.log('async2');  //6、
}

console.log('script staer');   // 1、同步任务开始
setTimeout(function(resolve) { //2、异步任务 -》 宏任务 暂时不执行   //12、异步任务 宏任务开始执行 
    console.log('setTimeout');
}, 0)
async1();   //3、同步任务

new Promise(function(resolve) { //7、同步任务
    console.log('promise');
    resolve();     //8、异步操作（微任务） .then 中存储的其对应方法
}).then(function() {   
    console.log('promise2');  //11、
});
console.log('script end');  //9、所有同步任务执行完 去Event Queue 中执行异步任务 

//script staer
//async1 start
//async2
//promise
//script end
//async1 end
//promise2
//setTimeout
```







第二个面试题



误区在于 认为await下面的任务应该都执行完 其实不是 和外面 一样 遇到异步任务也是先放在Event Queue中



```js
// 1 5 3 10  4 7 8 9 2
console.log(1); //1、
setTimeout(_ => { console.log(2); }, 1000)   //7、时间还没到 不执行   17.时间到达开始执行
async function fn() {
    console.log(3);  // 5、 执行到此处 将定时器放入任务队列中
    setTimeout(_ => { console.log(4); }, 20); //8、到达时间执行
    return Promise.reject() // 9.reject任务不执行
}
async function run() {
    console.log(5);//3、
    await fn();  //4、
    console.log(6); //10、不会被执行
}
run() //2、
    // console.time()  65ms
for (let i = 0; i < 90000000; i++) {}
// console.timeEnd()
setTimeout(_ => {  //5.5定时器 放在任务队列里  11.时间到达执行
    console.log(7);  //12.
    new Promise(resolve => {
        console.log(8); //13.
        resolve(); // 14.异步任务 放入队列里不执行 15//同步任务执行完 继续执行异步任务 
    }).then(_ => { console.log(9); });  // 16.
}, 0);
console.log(10); // 6、开始执行异步任务
```

