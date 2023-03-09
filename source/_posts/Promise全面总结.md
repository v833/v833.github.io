---
title: Promise全面总结
date: 2021-06-30 23:18:57
tags: javascript
swiper: true
swiperDesc: 'Prmoise诞生的原因
</br>Promise的生命周期
</br>Promse的基本用法
'
top: true
categories: 前端
---

>Promise是在ES6中新增的一种用于解决异步编程的方案，接下来会从下面几个方面详细介绍Promise
>
>Prmoise诞生的原因
>
>Promise的生命周期
>
>Promse的基本用法

{% title h2, Prmoise诞生的原因 %}
Promise诞生以前，在处理一个异步请求时，我们通常是在回调函数中做处理，例如处理一个Ajax请求的代码如下所示。


```js
    $.ajax({
      url: 'testUrl',
      success: function() {
        // 回调函数
      }
    })    
```

假如在一个行为中，需要执行多个异步请求，每一个请求又需要依赖上一个请求的结果，按照回调函数的处理方法，代码如下所示。

```js
    $.ajax({
      url: 'testUrl',
      success: function () {
        // 第二个请求
        $.ajax({
          url: 'url2',
          success: function () {
            // 第三个请求
            $.ajax({
              url: 'url2',
              success: function () {
                // 第四个请求
                $.ajax({
                  url: 'url2',
                  success: function () {
                    // 回调
                  }
                })
              }
            })
          }
        })
      }
    })
```


事实上，一个行为所产生的异步请求可能比这个还要多，这就会导致代码的嵌套太深，引发“回调地狱”。

“回调地狱”存在以下几个问题。

· 代码臃肿，可读性差。

· 代码耦合度高，可维护性差，难以复用。

· 回调函数都是匿名函数，不方便调试。

那么有什么方法能够避免在处理异步请求时，产生“回调地狱”的问题呢？

Promise就应运而生了，它为异步编程提供了一种更合理、更强大的解决方案。

{% title h2, Promise的生命周期 %}
每一个Promise对象都有3种状态，即pending（进行中）、fulfilled（已成功）和rejected（已失败）。

Promise在创建时处于pending状态，状态的改变只有两种可能。

一种是在Promise执行成功时，由pending状态改变为fulfilled状态；

另一种是在Promise执行失败时，由pending状态改变为rejected状态。

>**状态一旦改变，就不能再改变**，状态改变一次后得到的就是Promise的终态。

{% title h2, Promise 的基本用法 %}
Promise对象本身是一个构造函数，可以通过new操作符生成Promise的实例。


```js
    const promise = new Promise((resolve, reject) => {
      // 异步请求处理
      if (...) {
        resolve()
      } else {
        reject()
      }
    })
```

Promise执行的过程是：在接收的函数中处理异步请求，然后判断异步请求的结果

如果结果为“true”，则表示异步请求执行成功，调用resolve()函数，resolve()函数一旦执行，Promise的状态就从pending变为fulfilled；

如果结果为“false”，则表示异步请求执行失败，调用reject()函数，reject()函数一旦执行，Promise的状态就从pending变为rejected。

resolve()函数和reject()函数可以传递参数，作为后续.then()函数或者.catch()函数执行时的数据源。
需要注意的是Promise在创建后会立即调用，然后等待执行resolve()函数或者reject()函数来确定Promise的最终状态。


```js
    let promise = new Promise((resolve, reject) => {
      console.log('Promise');
      resolve()
    })
    promise.then(() => console.log('resolved'))
    console.log('Hello');
```

在上面的代码中，会先后输出“Promise” “Hello” “resolved”。

· 首先是Promise的创建，会立即执行，输出“Promise”。

· 然后是执行resolve()函数，这样的话就会触发then()函数指定回调函数的执行，但是它需要等当前线程中的所有同步代码执行完毕，因此会先执行最后一行同步代码，输出“Hello”。

· 最后是当所有同步代码执行完毕后，执行then()函数，输出“resolved”。
在拥有Promise异步解决方案后，实现原生get类型的Ajax请求的代码如下所示。


```js
    // 封装原生get类型Ajax请求
    function ajaxGetPromise(url) {
      const promise = new Promise((resolve, reject) => {
        const handler = function () {
          if (this.readyState !== 4) return
          if (this.status === 200) {
            resolve(this.response)
          } else {
            reject(new Error(this.statusText))
          }
        }
        const client = new XMLHttpRequest()
        client.open('GET', url)
        client.onreadystatechange = handler
        client.responseText = 'json'
        client.send()
      })
      return promise
    }
```

当一个Promise的实例创建好后，我们该如何进行成功或者失败的异步处理呢？

**这就需要调用then()函数和catch()函数了。**


{% title h3, then()函数 , warning %}
Promise在原型属性上添加了一个then()函数，表示在Promise实例状态改变时执行的回调函数。

它接收两个函数作为参数

第一个参数表示的是Promise在执行成功后（即调用了resolve()函数），所需要执行的回调函数，函数参数就是通过resolve()函数传递的参数。

第二个参数是可选的，表示的是Promise在执行失败后（即调用了reject()函数或抛出了异常），执行的回调函数。

以上面封装Ajax请求的函数为例，我们看看then()函数的用法。

```js
    ajaxGetPromise('/testUrl').then((response) => {
      console.log(response);
    })
```
ajaxGetPromise()函数在执行后会返回一个Promise实例，在执行then()函数时，回调函数中接收一个response参数，值为resolve()函数中传递的this.response，表示Ajax请求的响应。

then()函数返回的是一个新Promise实例，因此可以使用链式调用then()函数，在上一轮then()函数内部return的值会作为下一轮then()函数接收的参数值。

基于then()函数的链式调用写法，可以解决本小节开头提到的“回调地狱”问题。从代码风格上看，使用Promise的写法非常优雅。

需要注意的是，在then()函数中不能返回Promise实例本身，否则会出现Promise循环引用的问题，抛出异常。


{% title h3, catch()函数 , warning %}
catch()函数与then()函数是成对存在的，then()函数是Promise执行成功之后的回调，而catch()函数是Promise执行失败之后的回调，它所接收的参数就是执行reject()函数时传递的参数。

我们可以通过在Promise中手动抛出一个异常，来测试catch()函数的用法。


```js
    const promise = new Promise((resolve, reject) => {
      try {
        throw new Error('test')
      } catch (err) {
        reject(err)
      }
    })

    promise.catch(err => console.log(err)) // Error: test
```

因为promise实例在创建后会立即执行，所以进入try语句后会抛出一个异常，从而被catch()函数捕获到，在catch()函数中调用reject()函数，并传递Error信息。一旦reject()函数被执行，就会触发promise实例的catch()函数，从而能在catch()函数的回调函数中输出err的信息。

事实上只要在Promise执行过程中出现了异常，就会被自动抛出，并触发reject(err)，而不用我们去使用try...catch，在catch()函数中手动调用reject()函数。
因此前面的代码可以改写成如下所示的代码。


```js
    const promise = new Promise((resolve, reject) => {
        throw new Error('test')
    })

    promise.catch(err => console.log(err)) // Error: test
```

>需要注意的是，如果一个Promise的状态已经变成fulfilled成功状态，再去抛出异常，是无法触发catch()函数的。这是因为Promise的状态一旦改变，就会永久保持该状态，不会再次改变。


**在ES6中不仅为Promise的原型对象添加了then()函数和catch()函数等异步处理函数，还为Promise对象自身添加了一系列的静态函数，用来处理多Promise实例同时运行的情况。接下来我们选择几个重点的静态函数来讲解。**

{% title h3, Promise.all()函数 , warning %}
then()函数和catch()函数是Promise原型链中的函数，因此每个Promise的实例可以进行共享，而all()函数是Promise本身的静态函数，用于将多个Promise实例包装成一个新的Promise实例。

```js
const p = Promise.all([p1, p2, p3]);
```
返回的新Promise实例p的状态由3个Promise实例p1、p2、p3共同决定，总共会出现以下两种情况。

· 只有p1、p2、p3全部的状态都变为fulfilled成功状态，p的状态才会变为fulfilled状态，此时p1、p2、p3的返回值组成一个数组，作为p的then()函数的回调函数的参数。

· 只要p1、p2、p3中有任意一个状态变为rejected失败状态，p的状态就变为rejected状态，此时第一个被reject的实例的返回值会作为p的catch()函数的回调函数的参数。

>需要注意的是，作为参数的Promise实例p1、p2、p3，如果已经定义了catch()函数，那么当其中一个Promise状态变为rejected时，并不会触发Promise.all()函数的catch()函数。
>
>如果想要Promise.all()函数能触发catch()函数，那么就不要在p1、p2实例中定义catch()函数。
{% title h3, Promise.race()函数 , warning %}

Promise.all()函数作用于多个Promise实例上，返回一个新的Promise实例，表示的是如果多个Promise实例中有任何一个实例的状态发生改变，那么这个新实例的状态就随之改变，而最先改变的那个Promise实例的返回值将作为新实例的回调函数的参数。

```js
const p = Promise.race([p1, p2, p3]);
```
当p1、p2、p3这3个Promise实例中有任何一个执行成功或者失败时，由Promise.race()函数生成的实例p的状态就与之保持一致，并且最先那个执行完的实例的返回值将会成为p的回调函数的参数。

使用Promise.race()函数可以实现这样一个场景：假如发送一个Ajax请求，在3秒后还没有收到请求成功的响应时，会自动处理成请求失败。

实现的思路如下。

· 将Ajax请求处理成一个Promise，称之为p1。

· 创建一个自定义的Promise实例，称之为p2，在p2中通过setTimeout()函数控制3秒后抛出一个异常。

· 将p1和p2两个实例放入Promise.race()函数中，生成一个新的实例p，如果在3秒内接收到Ajax请求的返回值，表示实例p1执行成功，则p通过调用then()函数可以接收到p1的返回值；如果在3秒后还没有接收到Ajax请求的返回值，则会执行p2中的setTimeout()函数，抛出一个异常，表示p2执行失败，则p通过调用catch()函数可以接收到p2的返回值。

根据以上分析，可以得到以下代码。

```js
    const p1 = ajaxGetPromise('./testUrl')
    const p2 = new Promise((resolve, reject) => {
      setTimeout(() => reject(new Error('timeout')), 3000)
    })
    const p = Promise.race([p1, p2])
```

{% title h3, Promise.resolve()函数 , warning %}
Promise提供了一个静态函数resolve()，用于将传入的变量转换为Promise对象，它等价于在Promise函数体内调用resolve()函数。

Promise.resolve()函数执行后，Promise的状态会立即变为fulfilled，然后进入then()函数中做处理。



```js
    Promise.resolve('hello')
    // 等价于
    new Promise(resolve => resolve('hello'))
```
{% title h3, Promise.reject()函数 , warning %}
Promise.reject()函数用于返回一个状态为rejected的Promise实例，函数在执行后Promise的状态会立即变为rejected，从而会立即进入catch()函数中做处理，等价于在Promise函数体内调用reject()函数。

```js
const p = Promise.reject('出错了');
// 等价于
const p = new Promise((resolve, reject) => reject('出错了'));
```


在Promise. reject (param)函数中传递的参数param，会作为后续catch()函数的回调函数接收的参数。

```js
Promise.reject('fail').catch(result => console.log(result));
```

执行上面的代码后，会输出字符串“fail”。