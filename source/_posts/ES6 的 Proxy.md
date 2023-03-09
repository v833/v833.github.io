---
title:  ES6 的 Proxy
date: 2021-06-23 17:41:02
tags: javascript
swiper: true
swiperDesc: 'Proxy基本用法和应用场景'
categories: 前端
---
>ES6中新增了Proxy对象，从字面量上看可以理解为代理器，主要用于**改变对象的默认访问行为**，实际表现是在访问对象之间增加了一层**拦截**，任何对对象的访问行为都会通过这层拦截。在拦截中，我们可以**增加自定义的行为**。

{% title h3, Proxy的基本语法： , warning %}
```js
let proxy = new Proxy(target, handler)
```
target —— 是要包装的对象，可以是任何东西，包括函数。

handler —— 代理配置：带有“捕捉器”（“traps”，即拦截操作的方法）的对象。比如 get 捕捉器用于读取 target 的属性，set 捕捉器用于写入 target 的属性，等等。

**对 proxy 进行操作，如果在 handler 中存在相应的捕捉器，则它将运行**，并且 Proxy 有机会对其进行处理，否则将直接对 target 进行处理。

{% title h3, Proxy实例 , warning %}
```js
//我们用 get 来实现一个对象的默认值。
//我们将创建一个对不存在的数组项返回 0 的数组。
//通常，当人们尝试获取不存在的数组项时，他们会得到 undefined，
//但是我们在这将常规数组包装到代理（proxy）中，以捕获读取操作，并在没有要读取的属性的时返回 0

// 定义目标数组
let numbers = [0, 1, 2];

// 定义配置对象
let handler = {
  get(target, prop) {
    return prop in target ? target[prop] : 0
  }
}
// 生成Proxy实例
let numbersProxy = new Proxy(numbers, handler)

// 执行结果
console.log(numbersProxy[1]); // 1
console.log(numbersProxy[123]); // 0（没有这个数组项）
```

上面实例中，我们定义了一个包含get()函数的配置对象，表示的是对代理对象的属性进行读取操作，就会触发get()函数。因此在执行numbersProxy[1]，即访问Proxy实例数组项为1的值，会触发get()函数，在控制台输出自定义的结果。
{% title h3, 使用Proxy时的注意事项 , warning %}

>**必须通过代理实例访问**
>
>如果需要配置对象的拦截行为生效，那么必须是对代理实例的属性进行访问，而不是直接对目标对象进行访问。

{% title h3, Proxy实例函数及其基本使用 , warning %}
在上面例子中，我们通过访问代理对象的属性来触发自定义配置对象的get()函数。而get()函数只是Proxy实例支持的总共13中函数中的一种，这13中函数汇总如下。

{% title h4, get(target，property，receiver) , red %}
用于拦截对象的读取属性操作。例如调用proxy.name或者proxy[name]，其中target表示的是目标对象，property表示的是读取的属性值，receiver表示的是配置对象(最初被调用的对象。通常是 proxy 本身)。

{% title h4, set(target，property，value，receiver) , red %}
拦截对象属性的写入操作，即设置属性值，例如proxy.name = 'xx'或者proxy[name] = 'xx'，其中target表示目标对象，property表示的是将要设置的属性，value表示将要设置的属性值，receiver表示的是配置对象。

{% title h4, has(target，prop) , red %}
拦截hasProperty的操作，返回一个布尔值，最典型的表现形式是执行prop in target，其中target表示目标对象，prop表示判断的属性值。

{% title h4, deleteProperty(target，property) , red %}
拦截delete proxy[property]的操作，返回一个布尔值，表示是否执行成功，其中target表示目标对象，property表示将要删除的属性。

{% title h4, ownkeys(target) , red %}
拦截Object.getOwnPropertyNames(target)、Object.getOwnPropertySymbols(target)、Object.keys(target)、for...in循环等操作，其中target表示的是获取对象自身所有的属性名。
{% title h5, 拓展 , %}
Object.getOwnPropertyNames(obj) 返回非 Symbol 键。

Object.getOwnPropertySymbols(obj) 返回 Symbol 键。

Object.keys/values() 返回带有 enumerable 标志的非 Symbol 键/值。

for..in 循环遍历所有带有 enumerable 标志的非 Symbol 键，以及原型对象的键。

{% title h4, getOwnPropertyDecriptor(target，prop) , red %}
拦截Object.getOwnPropertyDecriptor(proxy, prop)操作，返回属性的属性描述符构成的对象，其中target表示目标对象，prop表示需要获取属性描述符集合的属性。

{% title h4, defineProperty(target，property，descriptor) , red %}
拦截Object.defineProperty(proxy, property, decriptor)、Object.defineProperties(proxy, decriptors)操作，返回一个布尔值，其中target表示目标对象，property表示新增的属性，descriptor表示的是属性描述符对象。

{% title h4, preventExtensions(target) , red %}
拦截Object.preventExtensions(proxy)操作，返回一个布尔值，表示的是让一个对象变得不可扩展，不能再增加新属性，其中target表示目标对象。

{% title h4, isExtensible(target) , red %}
拦截Object.isExtensible(proxy)，返回一个布尔值，表示对象是否可拓展。其中target表示目标对象。

{% title h4, getPrototypeOf(target) , red %}
拦截Object.getPrototypeOf(proxy)操作，返回一个对象，表示的是拦截获取对象原型属性，其中target表示目标对象。

{% title h4, setPropertyOf(target，prototype) , red %}
拦截Object.setPrototype(proxy, prototype)操作，返回一个布尔值，表示的是拦截设置对象的原型属性的行为，其中target表示目标对象，prototype表示新的原型对象。

{% title h4, apply(target，object，args) , red %}
拦截Proxy实例作为函数调用的操作，例如proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)，其中target表示目标对象，object表示函数的调用方，args表示函数调用传递的参数。

{% title h4, constructor(target，args) , red %}
拦截Proxy实例作为构造函数调用的操作，例如new Proxy(...args)，其中target表示目标对象，args表示函数调用传递的参数。
>这些函数都有一个通用的特性，即如果再target中使用了this关键字，再通过Proxy处理后，**this关键之指向的是Proxy实例，而不是目标对象target**。

```js
const person = {
  getName() {
    console.log(this === proxy)
  }
}

const proxy = new Proxy(person, {})
person.getName() // fasle
proxy.getName() // true
```

{% title h3, Proxy应用场景 , warning %}

{% title h4, 读取不存在属性 , red %}
通常，尝试读取不存在的属性会返回 undefined。

创建一个代理，在尝试读取不存在的属性时，该代理抛出一个错误。

这可以帮助及早发现编程错误。

```js
let user = {
  name: "xx"
};

function wrap(target) {
  return new Proxy(target, {
    get(target, prop) {
      if (prop in target) {
        return target[prop]
      } else {
        throw new ReferenceError(`Property doesn't exist: "${prop}"`)
      }
    }
  });
}

user = wrap(user);

console.log(user.name); // xx
console.log(user.age); // ReferenceError: Property doesn't exist: "age"
```

{% title h4, 读取负索引的值 , red %}
数组的索引值时从0开始依次递增的，正常情况下我们无法读取负索引的值，但是通过**Proxy的get()函数**可以做到这一点。
>负索引实际就是从数组的尾部元素开始，从后往前，寻找元素的位置。
>
>**换句话说，array[-N] 与 array[array.length - N] 相同。**

```js
let arr = [1, 2, 3, 4, 5]
arr = new Proxy(arr, {
  get(target, index) {
    if (index > 0) {
      return target[index]
    } else {
      return target[+index + target.length]
    }
  }
})
console.log(arr[1]) // 2
console.log(arr[2]) // 3
console.log(arr[-1]) // 5
console.log(arr[-2]) // 4   
```

{% title h4, 禁止访问私有属性 , red %}
在一些约定熟成的写法中，私有属性都会以下划线(_)开头，事实上，我们并不希望用户能访问到私有属性。

***我们将需要以下捕捉器*：**

`get 读取此类属性时抛出错误`

`set 写入属性时抛出错误`

`deleteProperty 删除属性时抛出错误`

`has 在使用 in 方法时排除以 _ 开头的属性`

`ownKeys 在使用 for..in 和像 Object.keys 这样的的方法时排除以 _ 开头的属性`


```js
let user = {
  name: "xx",
  _age: "18"
};

user = new Proxy(user, {
  get(target, prop) { // 拦截属性读取
    if (prop.startsWith('_')) {
      throw new Error("Access denied")
    } else {
      return target[prop]
    }
  },
  set(target, prop, val) { // 拦截属性写入
    if (prop.startsWith('_')) {
      throw new Error("Access denied")
    } else {
      target[prop] = val
      return true
    }
  },
  deleteProperty(target, prop) { // 拦截属性删除
    if (prop.startsWith('_')) {
      throw new Error("Access denied")
    } else {
      delete target[prop]
      return true
    }
  },
  has(target, prop) {
    if (prop.startsWith('_')) { // 拦截 in 操作
      throw new Error("Access denied");
    } else {
      return prop in target
    }
  },
  ownKeys(target) { // 拦截读取属性列表
    return Object.keys(target).filter(key => !key.startsWith('_'))
  }
});

// "get" 不允许读取 _age
try {
  user._age // Error: Access denied
} catch(e) { console.log(e.message) }

// "set" 不允许写入 _age
try {
  user._age = "20" // Error: Access denied
} catch(e) { console.log(e.message) }

// "deleteProperty" 不允许删除 _age
try {
  delete user._age // Error: Access denied
} catch(e) { console.log(e.message) }

// "has" 将 _age 过滤出去
  try {
    '_age' in user
  } catch (e) { console.log( e.message ) }
  
// "ownKeys" 将 _age 过滤出去
for(let key in user) console.log(key) // name
```

{% title h4, Proxy访问属性的限制 , red %}
当我们期望使用Proxy对对象的属性进行代理，并修改属性的返回值时，我们需要这个属性**不能同时为不可配置和不可写**。如果这个属性同时为不可配置和不可写，那么在通过代理读取属性时，会抛出异常。

```js
let target = Object.defineProperties({}, {
  // 可配置的name
  name: {
    value: 'xx',
    configurable: true,
    writable: false
  },

  // 不可配置的age
  age: {
    value: 18,
    configurable: false,
    writable: false
  }
})

target = new Proxy(target, {
  get(target, prop) {
    return 'abc'
  }
})

console.log(target.name) // abc
console.log(target.age) // Uncaught TypeError: 'get' on proxy: property 'age' is a read-only and non-configurable data property on the proxy target but the proxy did not return its actual value (expected '18' but got 'abc')
```

{% title h4, 拦截属性赋值操作 , red %}
定义一个person对象，包含一个age属性，取值在0~100之间，只要设置的值不在区间内，就会抛出异常。


```js
let user = {
  name: 'xx',
  age: 18
}

user = new Proxy(user, {
  set(target, prop, number) {
    if (prop === 'age' && number > 100 || number < 0) {
      throw new Error("The age is invalid")
    }
    target[prop] = number
  }
})

user.name = 'yy'
console.log(user.name) // yy
user.age = 20
console.log(user.age) // 20
user.age = 200
console.log(user.age) // Uncaught Error: The age is invalid
```

{% title h4, 函数的拦截 , red %}
Proxy中提供了apply()函数，用于拦截函数调用的操作，**函数调用包括直接调用、call()函数调用、apply()函数调用`3`种方式**。

通过对函数调用的拦截，可以加入自定义操作，从而得到新的函数处理结果。

```js
function sum (num1, num2) {
  return num1 + num2
}

sum = new Proxy(sum, {
  apply(target, obj, args) {
    return target.apply(obj, args) * 2
  }
})

console.log( sum(1, 2) ) // 6
console.log( sum.call(null, 1, 2) ) // 6
console.log( sum.apply(null, [1, 2]) ) // 6

```