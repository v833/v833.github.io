---
title: ES6 的 Reflect
date: 2021-06-24 17:32:32
tags: javascript
swiper: true
swiperDesc: 'Reflect基本用法和应用场景
'
categories: 前端
---
{% title h2, Reflect概述 %}

>什么是`Reflect`对象？
>
>我们可以这样理解，有一个名为`Reflect的全局对象`，上面挂载了对象的某些特殊函数(如：`[[Get]]`、`[[Set]]`)，这些函数可以通过类似于`Reflect.apply()`这种形式调用，所有在Reflect对象上的函数要么可以在`Object原型链`中找到，要么可以通过`命令式操作符`实现，例如`delete`和`in`操作符。

***既然在ES6以前，Object对象中已经有与Reflect的函数相同功能的函数或者命令式操作符，那么为什么还要在ES6中专门增加一个Reflect对象呢？***

主要原因有以下几点。

- `更合理地规划于Object对象相关的API`
在ES6中，Object对象的一些明显属于**语言内部的函数**都会添加到Reflect对象中，这样Object对象与Reflect对象中会存在相同处理函数。**而在未来的设计中，语言内部的函数只会添加到Reflect对象中**。

- `全局对象(内置的对象)`
用一个单一的全局对象去存储这些函数，能够保持其他的Javascript代码的整齐、干净。不然的话，这些函数可能是全局的，或者通过原型来调用，**不方便统一管理**。

- `代替命令式操作符`
将一些命令式操作符如delete、in等使用函数来代替，这样的目的是为了**代码更好维护，更好向下兼容**，同时避免出现更多的保留字。

```js
// 传统写法
'toString' in Object // true

// 新写法
Reflect.has(Object, 'toString') // true
```

- `修改Object对象的某些函数的返回结果`
修改Object对象的某些函数的返回结果，可以让其变得更合理，使得代码更好维护。

如果一个对象obj是**不能扩展的**，那么在调用ObjectdefineProperty(obj, prop, desc)时，会**抛出一个异常**。因此在传统的写法中，我们需要通过try...catch处理。

而使用Reflect.defineProperty(obj, prop, desc)时，返回的是**false**，新写法就可以通过if...else实现。

```js
// 传统写法
try {
  Object.defineProperty(obj, prop, desc) 
    // success
  } catch (e) {
    // failure
  }

// 新写法
if ( Reflect.defineProperty(obj, prop, desc) ) {
  // success
} else {
  // failure
}
```
- `Reflect对象的函数与Proxy对象的函数一一对应`
**只要是Proxy对象的函数，就能在Reflect对象上找到对应的函数**。这就让Proxy对象可以方便的调用对应的Reflect对象上的函数，完成默认行为，并以此为修改行为的基础。

也就是说，不管Proxy对象怎么修改默认行为，总可以在Refect对象上获取默认行为。事实上**Proxy对象也经常随着Reflect对象一起进行调用**。
{% title h2, Reflect静态函数 %}

**Reflect对象本身并不是一个构造函数(无法通过 new 调用)**，而是直接提供静态函数以供调用，Reflect对象的静态函数一共13个，如下所示。
- `Reflect.get(target, prop, receiver)`
Reflect.get()函数的作用是**获取对象的属性值**，等同于执行target[prop]
receiver表示函数中this绑定的对象。

- `Reflect.set(target, prop, value, receiver)`
Reflect.set()的作用是**设置某个属性值**。等同于执行target[prop] = value。
- `Reflect.apply(target, thisArg, args)`
Reflect.apply()函数的作用是**通过指定的参数列表执行target函数**，等同于执行Function.prototype.apply.call(target, thisArg, args)。其中target表示的是目标对象，thsArg表示的是执行target函数时的this对象，args表示的时参数列表。

- `Reflect.has(target, prop)`
Reflect.has()函数的作用是**判断属性是否在对象中**，等同于执行prop in target


- `Reflect.deleteProperty(target, prop)`
Reflect.deleteProperty()函数的作用是**删除对象的属性**，等同于执行delete obj[prop]

- `Reflect.ownKeys(target)`
Reflect.ownKeys()函数的作用是**获取对象的所有属性**，包括Symbol，等同于Object.getOwnPrototypeNames()与Objct.getOwnPrototypeSymbols()之和。

- `Reflect.construct(target, args)`
Reflect.construct()函数的作用是**执行构造函数**，等同于执行new Target(...args)

- `Reflect.defineProperty(target, prop, desc)`
Reflect.defineProperty()函数的作用是**为对象定义属性**，等同于执行Object.defineProperty()


- `Reflect.getOwnPropertyDescriptor(target, prop)`
Reflect.getOwnPropertyDescriptor()函数的作用是**得到制定属性的描述对象**，等同于执行Object.getOwnPropertyDescriptor()

- `Reflect.setPrototypeOf(target, newProto)`
Reflect.setPrototypeOf()函数的作用是**设置对象的原型**，等同于执行Object.setPrototypeOf(target, newProto)

- `Reflect.getPrototypeOf(target)`
Reflect.getPrototypeOf()函数的作用是**读取对象的__proto__属性**，等同于执行Object.getPrototypeOf(obj)


- `Reflect.isExtensible(target)`
Reflect.isExtensible()函数的作用是**判断对象是否可拓展**，等同于执行Object.isExtensible()

- `Reflect.preventExtensions(target)`
Reflect.preventExtensions()函数的作用是**让一个函数变得不可拓展**，等同于执行Object.preventExtensions()

>Reflect 上存在的一些静态函数对应于ES2015之前的Object上可用的方法。尽管某些方法在行为上看似相似，但它们之间常常存在细微的差异。
>
>[查看具体差异(MDN)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect/Comparing_Reflect_and_Object_methods)

{% title h2, Reflect 与 Proxy %}
**ES6在设计的时候就将Reflect和Proxy对象绑定在一起了**，Reflect对象的函数与Proxy对象的函数一一对应，因此在Proxy对象中调用Reflect对象对应的函数是一个明智的选择。

例如我们使用Proxy对象拦截属性的读取、设置和删除操作，并配合Reflect对象实现时，可以编写如下代码。


```js
let target = {
  name: 'xx'
}

target = new Proxy(target, {
  get(target, prop) {
    console.log( `读取属性${prop}的值为${target[prop]}` )
    return Reflect.get(target, prop)
  },

  set(target, prop, value) {
    console.log( `设置属性${prop}的值为${value}` )
    return Reflect.set(target, prop, value)
  },

  deleteProperty(target, prop) {
    console.log( `删除属性${prop}` )
    return Reflect.deleteProperty(target, prop)
  }
})

target.name // 读取属性name的值为xx
target.name = 'yy' // 设置属性name的值为yy
delete target.name // 删除属性name
```
>上面的实例只是讲解了如何配合使用Proxy对象和Reflect对象，那么两者的配合能实现什么样的功能呢？

**有一个最经典的案例就是可以实现观察者模式**。

{% title h3, Proxy 和 Reflect 实现观察者模式 , warning %}
>观察者模式的表现
>
>一个目标对象管理所有依赖它的观察者对象
>
>当自身的状态有改变时，会主动向所有观察者发出通知

```js
function makeObservable (target) {
  // 初始化 handler 存储
  target.observers = new Set()

  //将 handler 函数存储到 Set 中，以便于之后调用
  target.observe = handler => target.observers.add(handler)

  // 创建一个 proxy 以处理更改
  return new Proxy (target, {
    set(target, prop, value) {
      // 将操作转发给对象
      let success = Reflect.set(...arguments)
      if (success) {
        // 调用所有 handler
        target.observers.forEach(handler => handler(prop, value))
      }
      return success
    }
  })
}

let user = {};
user = makeObservable(user)
// 定义一个观察者
user.observe((key, value) => {
  console.log(`SET ${key}=${value}`)
});

user.name = "xx" // SET name=xx
user.age = 24 // SET age=24
```

{% title h3, 代理一个 getter , warning %}
让我们看一个示例，来说明为什么 **Reflect.get 更好**。此外，**我们还将看到为什么 get/set 有第三个参数 receiver，而且我们之前从来没有使用过它。**

我们有一个带有 _name 属性和 getter 的对象 user。

这是对 user 对象对一个代理（proxy）：

```js
let user = {
  _name: "xx",
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) {
    return target[prop];
  }
});

console.log(userProxy.name) // xx
```
>另一个对象 admin 从 user 继承后，我们可以观察到错误的行为

```js
let user = {
  _name: "xx",
  get name() {
    return this._name;
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) {
    return target[prop]  // (*) target = user
  }
});

let admin = {
  __proto__: userProxy,
  _name: "yy"
};

// 期望输出：yy
console.log(admin.name); // 输出：xx (!??)
```
**读取 admin.name 应该返回 "yy"，而不是 "xx"！**

发生了什么？或许我们在继承方面做错了什么？

但是，如果我们移除代理，那么一切都会按预期进行。

问题实际上出在代理中，**在 (*) 行。**

当我们读取 admin.name 时，由于 admin 对象自身没有对应的的属性，搜索将转到其原型。

原型是 userProxy。

从代理读取 name 属性时，get 捕捉器会被触发，并从原始对象返回 target[prop] 属性，在 (*) 行。

当调用 target[prop] 时，若 prop 是一个 getter，它将在 **this=target 上下文中运行其代码**。因此，结果是来自原始对象 target 的 this._name，即来自 **user**。

为了解决这种情况，我们需要 get 捕捉器的第三个参数 receiver。它保证将正确的 this 传递给 getter。在我们的例子中是 admin。

>如何把上下文传递给 getter？
>
>对于一个常规函数，我们可以使用 call/apply，但这是一个 **getter**，它不能“被调用”，只能被访问。

Reflect.get 可以做到。如果我们使用它，一切都会正常运行。

```js
let user = {
  _name: "xx",
  get name() {
    return this._name
  }
};

let userProxy = new Proxy(user, {
  get(target, prop, receiver) { // receiver = admin
    return Reflect.get(target, prop, receiver) // (*)
  }
});


let admin = {
  __proto__: userProxy,
  _name: "yy"
};

console.log(admin.name) // yy
```
现在 receiver 保留了对正确 this 的引用（即 admin），该引用是在 (*) 行中被通过 Reflect.get 传递给 getter 的。

***我们可以把捕捉器重写得更短*：**

```js
get(target, prop, receiver) {
  return Reflect.get(...arguments);
}
```