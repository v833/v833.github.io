---
title: 深入了解 javascript 中的 new 操作符
date: 2021-06-3 20:20:52
tags: javascript
swiper: true
swiperDesc: '构造函数中 this 的用法
</br>this 与 new 操作符的关系
</br>prototype 属性与 new 的关系
'
categories: 前端
---
{% note success, new 操作符在执行过程中会改变 this 的指向，所以我们在了解 new 操作符之前，我们先解释一下 this 的用法。 %}


{% title h2, 构造函数中 this 的用法 %}


```js
    function Person (name, age) {
      this.name = name
      this.age = age
    }

    console.log(new Person('xx', 18)); // Person {name: 'xx', age: 18}
```
输出结果中包含了 name 和 age 的信息。

事实上，我们并没有通过 return 返回任何值，为什么输出信息中会包含 name 和 age 属性呢？ 其中起作用的就是 this 这个关键字。

我们通过以下代码输出 this ，看看 this 具体的内容。

```js
    function Person (name, age) {
      console.log(this) // Peoson {}
      this.name = name
      this.age = age
    }

    new Person ('xx', 18)
```
我们发现 this 的实际值为 Person 空对象，后两句话就相当于给 Person 对象添加 name 和 age 属性，结果真的是这样吗？我们改写一下 Person 函数。

```js
    function Person (name, age) {
      console.log(this)
      Person.name = name
      Person.age = age
    }
    new Person ('xx', 18) // Person {}
```
输出结果中并未包含 name 和 age 属性。

因为在Javascript中，如果构造函数没有 return 值，则默认 return this。而上面代码中，this 实际是一个 Person 空对象，name 和 age 属性只是被添加到了临时变量 Person 中，为了能让输出结果包含 name 和 age 属性，我们将临时变量 Person 进行 return 就可以了。


```js
    function Person (name, age) {
      let Person = {}
      Person.name = name
      Person.age = age
      return Person
    }

    console.log(new Person ('xx', 18)) // {name: "xx", age: 18}
```
换句话说，new Person 做的就是类似的事情。

```js
function Person(name, age) {
  // this = {};（隐式创建）

  // 添加属性到 this
  this.name = name;
  this.age = age;

  // return this;（隐式返回）
}
```
通过以上分析，我们了解了构造函数中 this 的用法。
{% title h2, this 与 new 操作符的关系 %}


通过 new 操作符生成一个Person 对象的实例。

```js
let Person = new Person()
```
new 操作符做了 3 件事情。

```js
1.let person = {}
2.person.__proto__ = Person.prototype
3.Person.call(person)
```

我们自定义一个类似 new 功能的函数，来具体讲解上上面的 3 行代码。


```js
    function Person (name, age) {
      this.name = name
      this.age = age
    }

    function New () {
      let obj = {}
      Person.apply(obj, arguments)
      return obj
    }

    console.log(New('xx', 18)) // {name: "xx", age: 18}
```
返回的结果中也包含 name 和 age 属性，这就证明了 new 运算符对 this 指向的改变。Person.apply(obj, arguments) 调用后Person对象中的 this 就指向了 obj 对象，这样 obj 对象就具有了 name 和 age 属性。

**但是，不仅关注 new 操作符的函数本身，也要关注它的原型属性。**
{% title h2, prototype 属性与 new 的关系 %}

我们在 Person 的原型上添加一个 sayHi 函数，然后通过New()返回的对象去调用 sayHi 函数。

```js
    function Person (name, age) {
      this.name = name
      this.age = age
    }

    Person.prototype.sayHi = function () {
      console.log('Hi!');
    }
    
    console.log(New('xx', 18).sayHi()) // Uncaught TypeError: New(...).sayHi is not a function
```
 我们发现执行报错了，New 函数返回的对象并没有调用sayHi方法。

 这是因为 sayHi 方法是属于 Person 原型的函数，只有 Person 原型链上的对象才能继承 sayHi 方法。那么我们该怎么做呢?

 这里用到的就是__proto__属性，实例的__proto__属性指向创建实例的函数的原型。设置 obj 对象的__proto__值为 Person 对象的 prototype 属性，那么 obj 对象就继承了 Person 原型上的 sayHi 方法。


```js
    function Person (name, age) {
      this.name = name
      this.age = age
    }

    Person.prototype.sayHi = function () {
      console.log('Hi!');
    }
    
    function New () {
      // 另一种写法：let obj = Object.create(Person.prototype)
      let obj = {}
      obj.__proto__ = Person.prototype
      Person.apply(obj, arguments)
      return obj
    }
    
    console.log(New('xx', 18).sayHi()) // Hi
```
 结果输出 'Hi', 方法调用成功。