---
title: arguments 对象的性质和应用
date: 2021-06-4 19:04:42
tags: javascript
swiper: true
swiperDesc: 'arguments 对象的性质:</br>函数外部无法访问
  可通过索引访问
  由实参决定
</br>arguments 对象的应用
  实参的个数判断
  任意个数的参数处理
  模拟函数重载
'
categories: 前端
---
{% title h1, arguments 对象的性质, red %}
arguments 对象是所有函数都具有的一个内置局部变量，表示的是函数实际接收的参数，是一个**类数组**的结构。

之所以说 arguments 是类数组是因为它除了具有 length 属性外，不具有数组的其他属性。
{% title h2, 函数外部无法访问 %}
arguments对象只能在函数内部使用，无法在函数外部访问到 arguments 对象。同时 arguments 对象存在于函数作用域中，一个函数无法直接访问另一个函数的 arguments。
> 箭头函数没有 "arguments"
>
> 如果我们在箭头函数中访问 arguments，访问到的 arguments 并不属于箭头函数，而是属于箭头函数外部的“普通”函数。

```js
    console.log(typeof arguments); // undefined
    function fn () {
      console.log(arguments.length); // 4
    }
    fn (1, 2, 3, 4)
```
{% title h2, 可通过索引访问 %}
arguments 对象是一个类数组结构，可以通过索引访问，每一项表示对应传递的实参值，如果该项索引值不存在，则会返回 undefined。

```js
    function fn (num1, num2) {
      console.log(arguments[0]); // 1
      console.log(arguments[1]); // 2
      console.log(arguments[2]); // undefined
    }
    fn (1,2)
```
{% title h2, 由实参决定 %}
arguments 对象的值有实参决定，而不是由定义的形参决定，形参与 arguments 对象占用独立的内存空间。关于arguments 对象与形参之间的关系，可以总结为以下几点。

> 1、arguments 对象的 length 属性在函数调用的时候就已经确定，不会随着韩式的处理而变化。
>
> 2、指定的形参在传递实参的形况下，arguments 对象与形参值相同，并且可以互相改变。
>
> 3、指定的形参在未传递实参的情况下，arguments 对象对应索引值返回 undefined。
>
> 4、指定的形参在未传递实参的情况下，arguments 对象于形参值不能相互改变。


```js
    function fn (a, b, c) {
      console.log(arguments.length); // 2

      arguments[0] = 11;
      console.log(a); // 11

      b = 12;
      console.log(arguments[1]); // 12

      arguments[2] = 3;
      console.log(c); // undefined

      c = 13;
      console.log(arguments[2]); // 3
      
      console.log(arguments.length); // 2
    }
    fn (1,2)
```
通过上面的例子，很好的总结了 arguments 对象的性质。
## arguments 引发的问题：形参失去意义

我们看一个例子：

```js
    function fn (a, b, c) {
      arguments[0] = 11;
      console.log(a); // 11
      console.log(b); // 2
    }
    fn (1,2)
```
>**注意：永远不要直接修改 arguments 对象**
>
>如同上面的例子，arguments 对象与函数的形参的关系及其脆弱。修改 arguments 对象需要承担函数的形参失去意义的风险。在严格模式下，函数参数不支持对arguments对象的修改。
{% title h1, arguments 对象的应用, red %}
既然 arguments 有着致命的确定，那么我们该怎么应用它呢？我们来看下面几个应用。
{% title h2, 实参的个数判断 %}
定义一个函数，明确要求在调用时只能传递3个参数，如果传递的参数个数不等于3，则直接抛出异常。

```js
    function fn(a, b, c) {
      if (arguments.length !== 3) {
        throw new Error("传递的参数个数不为3")
      }
      // do something
    }
```
{% title h2, 任意个数的参数处理 %}
定义一个函数，该函数只会特定处理传递的前几个参数，对于后面的参数不论传递多少个都会统一处理。

例如：定义一个函数，需求将多个字符串使用分隔符相连，并返回一个结果字符串。此时第一个参数表示分隔符，后面所有的参数表示待相连符。

```js
    function joinStr (seperator) {
      // arguments 是一个类数组，并且不能直接操作 arguments
      // 可以通过call()函数间接调用 slice() 函数返回一个新的数组，操作这个数组
      let str = Array.prototype.slice.call(arguments, 1)
      return str.join(seperator)
    }

    joinStr('-', 'x', 'y', 'z') // x-y-z
    joinStr(',', 'o', 'p', 'q') // o,p,q
```
{% title h2, 模拟函数重载 %}
函数重载就是在程序中有命名一样的函数，通过参数的判断实现相同函数的不同调用。

javascript 不能实现函数重载，因为命名一样的函数，后面的会把前面的覆盖掉。我们可以使用 arguments 来模拟函数重载。

定义一个函数，实现多值求和。


```js
    function sum () {
      let arr = [...arguments]
      return arr.reduce((pre, cur) => {
        return pre + cur
      }, 0)
    }

    console.log(sum(1, 2, 3)); // 6
```