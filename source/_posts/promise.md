---
title: Promise
# cover: https://file.crazywong.com/gh/jerryc127/CDN@latest/cover/default_bg.png
---

在JavaScript的异步编程世界里，我们经常面对复杂的逻辑和难以追踪的错误。你是否曾经在编写JavaScript代码时，被层层嵌套的回调函数弄得焦头烂额？是否在处理并发异步任务时感到力不从心？如果有，那么Promise将是你的救星。它不仅简化了异步代码的结构，还增强了代码的可读性和可维护性。

在本篇文章中，我们将一步步揭开Promise的神秘面纱，深入探索Promise的魔力！

# 一、为什么需要Promise

-   Promise支持链式调用，可以解决回调地狱。

-   Promise指定回调函数的方式更灵活

    -   旧时:必须在启动异步任务前指定回调函数
    -   Promise：启动异步任务 => 返回Promise对象 => 给Promise对象绑定回调函数（甚至可以在异步任务结束后指定多个）

## 那么什么是回调地狱呢？

> ## **回调** **地狱**：
>
> 回调地狱（Callback Hell）是指在使用嵌套回调函数处理异步操作时，代码结构变得复杂和难以维护的问题。这种情况通常发生在需要顺序执行多个异步操作，并且每个操作都依赖于前一个操作的结果时。
>
> ### 回调地狱的表现
>
> 当多个异步操作需要嵌套在一起时，代码会不断向右缩进，形成一个“金字塔”或“箭头”的形状。代码变得难以阅读和维护，错误处理也变得复杂。
>
> 以下是一个典型的回调地狱示例：
>
> ```javascript
> loadScript('1.js', function(error, script) {
>   if (error) {
>     handleError(error);
>   } else {
>     // ...
>     loadScript('2.js', function(error, script) {
>       if (error) {
>         handleError(error);
>       } else {
>         // ...
>         loadScript('3.js', function(error, script) {
>           if (error) {
>             handleError(error);
>           } else {
>  // ...加载完所有脚本后继续
>           }
>         });
>
>       }
>     });
>   }
> });
> ```
>
> ### 回调地狱的问题
>
> 1.  **代码可读性差**：嵌套的回调函数使代码向右不断缩进，变得难以阅读和理解。
> 1.  **错误处理复杂**：每个嵌套层都需要单独处理错误，导致代码冗长且容易出错。
> 1.  **难以维护和扩展**：当需要添加新的异步操作或修改现有逻辑时，嵌套结构使得代码的修改变得复杂且容易引入新错误。

  


为了解决回调地狱，我们可以使用**Promise**或者**async/await**

> #### 1. **使用Promise**
>
> ```javascript
> doSomething()
>     .then(result1 => doSomethingElse(result1))
>     .then(result2 => doAnotherThing(result2))
>     .then(result3 => doSomethingMore(result3))
>     .then(result4 => {
>         // 最终的处理逻辑console.log(result4);
>     })
>     .catch(error => {
>         // 统一的错误处理console.error(error);
>     });
> ```
>
> #### 2. **使用** **async/await**
>
> `async/await` 是基于 Promise 的语法糖，提供了更简洁的方式来编写异步代码，使其看起来像同步代码。
>
> ```javascript
> async function process() {
>     try {
>         const result1 = await doSomething();
>         const result2 = await doSomethingElse(result1);
>         const result3 = await  doAnotherThing(result2);
>         const result4 = await doSomethingMore(result3);// 最终的处理逻辑
>         console.log(result4);
>     } 
>     catch (error) {
>         // 统一的错误处理console.error(error);
>     }
> }
> process();
> ```

# 二、Promise的基础概念

## 对Promise的简单理解：

-   概念：Promise是es6引入的进行**异步**编程的解决方案，是用于处理异步操作的一种对象。它通过三种状态（Pending、Fulfilled、Rejected）来管理异步操作，并提供 `then` 和 `catch` 方法来处理成功和失败的结果。Promise 主要解决了回调地狱问题，使代码更加清晰和易于维护。

-   具体表达：

    -   从语法上讲：Promise是一个**构造函数**
    -   从功能上讲：Promise对象用来**封装**一个异步操作并可以**获取**其**成功/失败**的结果值

-   作用：常用于封装ajax异步请求，解决回调地狱。

> 常见的异步编程有fs文件操作，数据库操作，AJAX，定时器

**举一个** **AJAX** **封装的例子：**

```javascript
   

        // 封装（传入url，返回Promise对象）
        function sendAJAX(url){
            return new Promise((resolve,reject) => {
                const xhr = new XMLHttpRequest();
                xhr.responseType = 'json';
                xhr.open('GET',url);
                xhr.send();
                xhr.onreadystatechange = () => {
                    if(xhr.readyState === 4){
                        if(xhr.readyState >=200 && xhr.status < 300){
                            //判断成功
                            resolve(xhr.response);
                        }
                        else{
                            //判断失败
                            reject(xhr.status);
                        }
                    }
                }
            })
        }

        // 调用
        sendAJAX('传入的url')
        .then(value => {
            console.log(value);
        },reason => {
            console.warn(reason);
        })
  
```

## **Promise的状态**：

### **三种状态**：

-   **Pending（等待中，未决定的）** ：初始状态，表示异步操作还未完成。
-   **Resolve/Fulfilled（已成功）** ：表示异步操作成功完成。
-   **Rejected（已失败）** ：表示异步操作失败。

### **两种状态的改变**：

1.  pending 变为 resolved
1.  pending 变为 rejected

> 说明: 只有这 2种，且一个 promise 对象只能改变一次.无论变为成功还是失败，都会有一个结果数据。
>
> 成功的结果数据一般称为 **value**，失败的结果数据一般称为 **reason**。

> 不可变性：一旦 Promise 状态改变（从 Pending 变为 Resolve/Fulfilled 或 Rejected），就无法再改变。

由 `new Promise` 构造器返回的 `promise` 对象具有以下内部属性：

-   `state` —— 最初是 `"pending"`，然后在 `resolve` 被调用时变为 `"fulfilled"`，或者在 `reject` 被调用时变为 `"rejected"`。
-   `result` —— 最初是 `undefined`，然后在 `resolve(value)` 被调用时变为 `value`，或者在 `reject(error)` 被调用时变为 `error`。

![](https://p0-xtjj-private.juejin.cn/tos-cn-i-73owjymdk6/8a015ab269ee497fa6c8addd0bdc939e~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg5LiA5Y-q5bm46L-Q55qE576K:q75.awebp?policy=eyJ2bSI6MywidWlkIjoiMzc4NDk5MDg4MjAxNDM1NiJ9&rk3s=f64ab15b&x-orig-authkey=f32326d3454f2ac7e96d3d06cdbb035152127018&x-orig-expires=1763530803&x-orig-sign=MAMMd%2F%2FYRNOU8SxKKS7wfJ7fWhw%3D)

### 内部机制

Promise 内部维护了一个**状态**和一个**回调** **队列**。当 Promise 的**状态变为** Resolve/Fulfilled 或 Rejected 时，会**将对应的** **回调函数** **加入事件队列**，等待 JavaScript 的事件循环处理。

简化版Promise核心原理实现：

```javascript
class MyPromise {
    constructor(executor) {
        this.state = 'pending';
        this.value = undefined;
        this.handlers = [];

        const resolve = (value) => {
            if (this.state !== 'pending') return;
            this.state = 'fulfilled';
            this.value = value;
            this.handlers.forEach(this.handle);
        };

        const reject = (reason) => {
            if (this.state !== 'pending') return;
            this.state = 'rejected';
            this.value = reason;
            this.handlers.forEach(this.handle);
        };

        executor(resolve, reject);
    }

    then(onFulfilled, onRejected) {
        return new MyPromise((resolve, reject) => {
            this.handle({
                onFulfilled,
                onRejected,
                resolve,
                reject
            });
        });
    }

    catch(onRejected) {
        return this.then(null, onRejected);
    }

    handle(handler) {
        if (this.state === 'pending') {
            this.handlers.push(handler);
        } else {
            if (this.state === 'fulfilled' && typeof handler.onFulfilled === 'function') {
                handler.onFulfilled(this.value);
            }
            if (this.state === 'rejected' && typeof handler.onRejected === 'function') {
                handler.onRejected(this.value);
            }
        }
    }
}

// 使用示例
const myPromise = new MyPromise((resolve, reject) => {
    setTimeout(() => resolve('成功结果'), 1000);
});

myPromise
    .then(result => {
        console.log(result); // 打印 "成功结果"
    })
    .catch(error => {
        console.error(error);
    });
```

# 三、创建和使用Promise
底层创建了**Promise构造函数**，并且给该构造函数绑定了**then**、**catch**、**finally**等**原型**方法，和**reject**、**resolve**、**all**、**race**等**静态**方法。

## **创建Promise**

```javascript
const p = new Promise((resolve, reject) => {
// 异步操作
    if ('成功') {
        resolve('成功结果');
    } 
    else {
        reject('错误信息');
    }
});
```

## **处理Promise**：绑定**then**、**catch**、**finally等**方法。

```javascript
  p.then(result => {// 处理成功结果
    })
    .catch(error => {// 处理错误
    })
    .finally(() => {// 无论成功或失败都执行
    });
```

## PromiseAPI

### 1.  Promise 构造函数：Promise (excutor){}

参数：一个executor函数。

executor 函数: 执行器(resolve, reject) => {}

-   resolve 函数: 内部定义成功时我们调用的函数 value =>{}
-   reject 函数: 内部定义失败时我们调用的函数 reason =>{}

说明: executor 会在 Promise 内部立即**同步调用**,异步操作在**执行器中执行**

### 2.  Promise.prototype.then 方法：(onResolved, onRejected)=> {}

参数：两个回调函数。

（1）onResolved函数: 成功的回调函数 (value) =>{}

（2）onRejected 函数: 失败的回调函数 (reason) =>{}

说明: 指定用于得到成功 value 的成功回调和用于得到失败 reason 的失败回调，返回一个新的 promise 对象

### 3.  Promise.prototype.catch 方法：(onRejected) =>{}

参数：一个失败的回调函数。

onRejected 函数：失败的回调函数(reason) =>{}

```javascript
        let  p = new Promise((resolve,reject) => {
            //修改promise对象的状态
            reject('error');
        })
        p.catch(reason => {
            console.log(reason);//'error'
        })
```

### 4.  Promise.prototype.finally 方法

参数：无参数

`finally` 的功能是设置一个处理程序在前面的操作完成后，执行清理/终结。

-   `finally` 处理程序没有得到前一个处理程序的结果（它**没有参数**）。在 finally 中，我们**不知道 promise 是否成功**。而这个结果被传递给了下一个合适的处理程序。
-   如果 `finally` 处理程序**返回**了一些内容，那么这些内容会被**忽略**。此规则的唯一例外是当 `finally` 处理程序抛出 error 时。此时这个 error（而不是任何之前的结果）会被转到下一个处理程序。
-   当 `finally` **抛出 error** 时，执行将转到最近的 error 的处理程序。

例如，停止加载指示器，关闭不再需要的连接等。

把它想象成派对的终结者。无论派对是好是坏，有多少朋友参加，我们都需要（或者至少应该）在它之后进行清理。

```javascript
new Promise((resolve, reject) => {
  /* 做一些需要时间的事，之后调用可能会 resolve 也可能会 reject */
})
 // 在 promise 为 settled 时运行，无论成功与否
  . finally ( () => stop loading indicator) // 所以，加载指示器（loading indicator）始终会在我们继续之前停止
  .then(result => show result, err => show error)
```

例如，在这结果被从 `finally` 传递给了 `then`：

```javascript
new Promise((resolve, reject) => {
  setTimeout(() => resolve("value"), 2000)
})
  .finally(() => alert("Promise ready")) // 先触发
  .then(result => alert(result)); // <-- .then 显示 "value"
  
  //第一个 promise 返回的 value 通过 finally 被传递给了下一个 then。
```

下面是一个 promise 返回结果为 error 的示例

```javascript
new Promise((resolve, reject) => {
  throw new Error("error");
})
  .finally(() => alert("Promise ready")) // 先触发
  .catch(err => alert(err));  // <-- .catch 显示这个 error
```

### 5.  Promise.resolve 方法: (value) =>{}

参数（value）：成功的数据或 promise 对象 说明: 返回一个成功/失败的 promise 对象

```javascript
        let p1 = Promise.resolve(521);//传入非Promise
        let p2 = Promise.resolve(new Promise((resolve,reject) => {
            // resolve('OK');//这里面是成功，外面也是成功
            reject('Error');//这里面是失败，外面也失败。如果外面没有一个对失败进行处理的，会报错

        }))
        //如果传入的参数为非promise类型的对象，返回结果为成功的promise对象
        //如果传入的参为promise对象，则参数的结果决定了resolve的结果
        console.log(p1);    //状态为成功，521
        console.log(p2);   //状态为成功，OK  或 状态为失败，Error,  如果没有下面对失败的处理会报错
        
        p2.catch(reason => {
            console.log(reason);
        })
```

### 6.  Promise.reject 方法：(reason) =>{}

参数（reason）: 失败的原因 说明: 返回一个失败的 promise 对象

返回的结果永远是失败的，你传入什么，失败的结果就是什么

```javascript
        //无论传入什么类型的数值，返回结果都是一个失败的promise对象
        let p1 = Promise.reject(521);
        let p2 = Promise.reject('iloveyou');
    
        let p3 = Promise.reject(new Promise((resolve,reject) => {
            resolve('OK');
        })) 
        console.log(p3);//状态是失败，失败的结果是传入的 成功的promise对象
```

### 7.  Promise.all 方法：(promises) =>{}

参数：n 个 promise 组成的数组

说明: 返回一个新的 promise ，只有所有的 promise 都成功才成功，只要有一个失败了就直接失败

-   成功：结果是所有 Promise 对象结果组成的数组
-   失败：结果是失败的 Promise 对象的结果

```javascript
        let p1 = new Promise((resolve,reject) => {
            resolve('OK');
        })
        // let p2 = Promise.resolve('Success');
        let p2 = Promise.reject('Error');
        let p3 = Promise.resolve('Oh Yeah');

        const result = Promise.all([p1,p2,p3]);
        console.log(result);//失败，返回的失败的结果值，是p2的结果值
        //成功则返回一个成功的数组
```

### 8.  Promise.allSettled 方法（新增）

当我们需要**所有**结果都成功时，**Promise.all**对这种“全有或全无”的情况很有用。

而当我们想要获取（fetch）多个用户的信息。即使其中一个请求失败，我们仍然对其他的感兴趣时，可以使用Promise.allSettled。

`Promise.allSettled` 等待所有的 promise 都被 settle，无论结果如何。结果数组会是这样的：

-   成功：结果数组对应元素的内容为 `{status:"fulfilled", value:result}`，
-   失败：结果数组对应元素的内容为 `{status:"rejected", reason:error}`。

```javascript
let urls = [
  'https://api.github.com/users/iliakan',
  'https://api.github.com/users/remy',
  'https://no-such-url'
];

Promise.allSettled(urls.map(url => fetch(url)))
  .then(results => { // (*)
    results.forEach((result, num) => {
      if (result.status == "fulfilled") {
        alert(`${urls[num]}: ${result.value.status}`);
      }
      if (result.status == "rejected") {
        alert(`${urls[num]}: ${result.reason}`);
      }
    });
  });
  
  
//上面的 (*) 行中的 results 将会是：
//[
//  {status: 'fulfilled', value: ...response...},
//  {status: 'fulfilled', value: ...response...},
//  {status: 'rejected', reason: ...error object...}
//]
```

### 9.  Promise.race 方法：(promises) =>{}

并行执行多个Promise，第一个完成的Promise决定结果。

参数：n 个 promise 组成的数组 说明: 返回一个新的 promise,**第一个完成的 promise 的结果状态**就是最终的结果状态（由最先改变Promise的状态决定）

```javascript
        let p1 = new Promise((resolve,reject) => {
            setTimeout(() => {
                resolve('OK');
            },1000);
        })
        let p2 = Promise.resolve('Success');
        let p3 = Promise.resolve('Oh yeah');

        //调用
        const result = Promise.race([p1,p2,p3]);

        //谁先改变状态，result的结果就是哪个的状态，p1一秒后才改变，所以p2最先改变状态，result的结果为p2的结果

        console.log(result);
```

### 10. Promise.any 方法

并行执行多个Promise，任何一个成功就成功。

与 `Promise.race` 类似，区别在于 `Promise.any` 只等待**第一个 fulfilled 的 promise**，并将这个 fulfilled 的 promise 返回。如果给出的 promise 都 rejected，那么返回的 promise 会带有 `AggregateError` —— 一个特殊的 error 对象，在其 `errors` 属性中存储着所有 promise error。

例：

```javascript
Promise.any([
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("Whoops!")), 1000)),
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 2000)),
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
]).then(alert); // 1
```

所有都失败：

```javascript
Promise.any([
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("Ouch!")), 1000)),
  new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000))
]).catch(error => {
  console.log(error.constructor.name); // AggregateError
  console.log(error.errors[0]); // Error: Ouch!
  console.log(error.errors[1]); // Error: Error!
});
```

我们在 `AggregateError` 错误类型的 error 实例的 `errors` 属性中可以访问到失败的 promise 的 error 对象。

## 几个相关问题
1.  #### 如何改变Promise状态

(1) **resolve(value)** : 如果当前是 pending 就会变为 resolved

(2) **reject(reason)** : 如果当前是 pending 就会变为 fulfilled（rejected）

(3) **抛出异常**: 如果当前是 pending 就会变为 rejected

```javascript
        let p = new Promise((resolve,reject) => {
            //1.resolve 函数     转为成功
            resolve('ok');   // pending => fulfilled(resolved)

            //2.reject 函数
            reject('error'); // pending => rejected
            
            //抛出错误
            throw '出问题了';
        })
        console.log(p);
```

 #### 2. 一个 promise 指定多个成功/失败回调函数，都会调用吗?

当 promise 改变为对应状态时都会调用

```javascript
        let p = new Promise((resolve,reject) => {
            resolve('OK');
        })

        //指定回调1
        p.then(value => {
            console.log(value);
        })

        //指定回调2
        p.then(value => {
            alert(value);
        })
        
        
        //都会调用
```

3.  #### 改变 promise 状态和指定回调函数谁先谁后?

(1) 都有可能，正常情况下是先指定回调再改变状态（因为Promise里面通常异步任务居多），但也可以先改状态再指定回调

> 其实是判断属于宏任务还是微任务，相关可以看看上一篇文章~
>
> [事件循环-宏任务与微任务](https://juejin.cn/post/7398038441524723746)

```javascript

        //同步，先执行promise改变状态，再指定回调
        //异步，then方法先执行  

        let p = new Promise((resolve,reject) => {
            //resolve改变状态
        
            // resolve('OK');  //同步 ：先改状态再指定回调
            setTimeout(() => {  //异步
                resolve('OK');
            },1000);        
        })

        p.then(value => {   //then方法指定回调

        },reason => {

        })
```

(2) 如何先改状态再指定回调?

-   在执行器中直接调用 resolve()/reject()
-   延迟更长时间才调用 then() （比如Promise执行器函数里异步函数是一秒后执行回调函数，下面的then方法可以加个两秒的定时器再执行）

(3) 什么时候才能得到数据?

也就是说回调函数什么时候执行

-   如果先指定的回调，那当**状态发生改变时**，回调函数就会调用，得到数据
-   如果先改变的状态，那当**指定** **回调** **时**，回调函数就会调用，得到数据

 #### 4. promise.then()返回的新 promise 的结果状态由什么决定?

(1) 简单表达: 由 then **指定的** **回调函数** **执行的结果**决定

(2)详细表达: ① 如果**抛出异常**，新 promise 变为 **rejected**， reason 为**抛出的异常** ② 如果返回的是**非 promise 的任意值**，新 promise变为**resolved**, value 为**返回的值**

③ 如果返回的是**另一个新 promise**，**此 promise 的结果**就会成为**新 promise 的结果**

```javascript
        let p = new Promise((resolve,reject) => {
            resolve('ok');
        })

        //执行then方法   
        let result = p.then(value => {
            // console.log(value);//返回结果是一个promise对象

            //1.抛出错误
            // throw '出了问题';

            //2.返回结果是非Promise类型的对象
            return 111; //状态会变为成功，且return的值就是成功的结果

            // 3.返回结果是promise对象
            // return new Promise((resolve,reject) => {
                // resolve('success');
                // reject('Error');
            // });
        },reason => {
            console.warn(reason);
        })
        console.log(result);
```

 #### 5. promise 如何串连多个操作任务?(**链式调用)**

(1) promise 的 then()返回一个新的 promise,可以开成 then（）的链式调用

(2) 通过 then 的**链式调用**串连多个同步/异步任务

```javascript
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000);

}).then(function(result) {

  alert(result); // 1
  return result * 2;

}).then(function(result) { 

  alert(result); // 2
  return result * 2;

}).then(function(result) {

  alert(result); // 4
  return result * 2;

});
```

这样之所以是可行的，是因为每个对 `.then` 的调用都会返回了一个新的 promise，因此我们可以在其之上调用下一个 `.then`。

当处理程序返回一个值时，它将成为该 promise 的 result，所以将使用它调用下一个 `.then`。

> 注意：我们也可以将多个 `.then` 添加到一个 promise 上。但这并不是 promise 链，它们不会相互传递 result；相反，它们之间彼此独立运行处理任务。
>
> 例如：
>
> ```javascript
> let promise = new Promise(function(resolve, reject) {
>     setTimeout(() => resolve(1), 1000);
> });
> promise.then(function(result) {
>     alert(result); // 1
>     return result * 2;
> });
> promise.then(function(result) {
>     alert(result); // 1
>     return result * 2;
> });
> promise.then(function(result) {
>     alert(result); // 1
>     return result * 2;
> });
> ```

  


另一个例子：

```javascript
        let p = new Promise((resolve,reject) => {
            setTimeout(() => {
                resolve('OK');
            },1000);
        })
        p.then(value => {//then 方法返回的结果是一个成功的promise
            return new Promise((resolve,reject) => {
                resolve('success');
            });
        }).then(value => {
           console.log(value); //得等一秒后p的状态变为成功，上面的回调才会执行，然后后边这个回调才会执行
        }).then(value => {
            console.log(value); //输出undefined then的返回结果是一个promise，而promise的结果由指定函数的返回值决定
        })
```

> .then返回结果是Promise，而Promise状态由它所指定的回调函数的**返回值**决定，倒数第二个then中的promise回调没有声明返回值，所以最后一个then输出undefined??????

#### 6.  promise 异常传透?

(1)当使用 promise 的 then 链式调用时，可以在最后指定失败的回调

(2) 前面任何操作出了异常，都会传到最后失败的回调中处理

> ```javascript
>         let p = new Promise((resolve,reject) => {
>             setTimeout(() => {
>                 resolve('OK');  //这个成功后才会调用后面的
>                 // reject('Err');
>             },1000);
>         });
>         p.then(value => {
>             // console.log(111);
>             throw '失败啦';
>         }).then(value => {
>             console.log(222);
>         }).then(value => {
>             console.log(333);
>         }).catch(reason => {    //只需在最后指定一个失败的函数就可以，而在中间不需要指定失败的回调，这就是异常的穿透
>             console.warn(reason);
>         })
> ```

 #### 7. 中断 promise 链?

(1) 当使用 promise 的 then 链式调用时，在中间中断，不再调用后面的回调函数

(2) 办法: 在回调函数中返回一个 pendding状态的 promise 对象

return new Promise(() => {});

```javascript
        let p = new Promise((resolve,reject) => {
            setTimeout(() => {
                resolve('OK');  //这个成功后才会调用后面的
            },1000);
        });
        p.then(value => {
            console.log(111);
            //现在只想让1输出，2,3， 就不执行了
            //有且只有一种方式  返回一个pending的promise对象
            return new Promise(() => {});
        }).then(value => {
            console.log(222);
        }).then(value => {
            console.log(333);
        }).catch(reason => {    //只需在最后指定一个失败的函数就可以，而在中间不需要指定失败的回调，这就是异常的穿透
            console.warn(reason);
        })
```

