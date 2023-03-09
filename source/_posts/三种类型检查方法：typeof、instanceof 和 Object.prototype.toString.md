---
title: 三种类型检查方法：typeof、instanceof 和 Object.prototype.toString
date: 2021-05-29 21:20:32
tags: javascript
swiper: true
swiperDesc: 'typeof 运算符
</br>instanceof运算符
</br>Object.prototype.toString 函数
'
categories: 前端
---

{% title h2, typeof 运算符, %}


typeof 运算符返回参数的类型。当我们想要分别处理不同类型值的时候，或者想快速进行数据类型检验时，非常有用。 它支持两种语法形式：

- 作为运算符：typeof x。
- 函数形式：typeof(x)。

括号有的时候时必须的，如果不加上括号将会因为优先级的问题得不到我们想要的结果。例如：

```javascript
typeof 'string' === true; // false
typeof ('string' === true); // "boolean"
复制代码
```

对 typeof x 的调用会以**字符串**的形式返回数据类型：

```javascript
typeof undefined // "undefined"

typeof 0 // "number"

typeof 10n // "bigint"

typeof true // "boolean"

typeof "foo" // "string"

typeof Symbol("id") // "symbol" 

typeof Math // "object"  (1) // 任何其他对象

typeof null // "object"  (2)

typeof alert // "function"  (3) // 函数对象
复制代码
```

最后三行可能需要额外的说明：

1. Math 是一个提供数学运算的内建 object。
2. typeof null 的结果是 "object"。这是官方承认的 typeof 的行为上的错误，这个问题来自于 JavaScript 语言的早期，并为了兼容性而保留了下来。null 绝对不是一个 object。null 有自己的类型，它是一个特殊值。
3. typeof alert 的结果是 "function"，因为 alert 在 JavaScript 语言中是一个函数。在 JavaScript 语言中没有一个特别的 “function” 类型。函数隶属于 object 类型。但是 typeof 会对函数区分对待，并返回 "function"。这也是来自于 JavaScript 语言早期的问题。从技术上讲，这种行为是不正确的，但在实际编程中却非常方便。

另外 typeof 处理包装类型的时候会返回对应的原始数据类型。但是不推荐这样写。例如：

```javascript
        typeof Number(1); // "number"
        typeof Boolean(true); // "boolean"
        typeof String('string'); // "string"
复制代码
```
{% title h3, typeof 运算符区别对待Object类型和Function类型, warning %}


从技术角度讲，函数在ECMAScript中是对象，不是一种数据类型。然而，函数也确实有一些特殊的属性，因此通过typeof 运算符来区分函数和其他对象是有必要的。
{% title h3, typeof 运算符对null的处理, warning %}

这个bug是第一版Javascript留下来的，javascript中不同对象在底层都表示为二进制，而javascript 中每种数据类型使用3bit表示，会把二进制前三位都为0的判断为object类型。

- 000 表示Object类型的数据。
- 001 表示Int类型的数据。
- 010 表示Double类型的数据。
- 100 表示String类型的数据。
- 110 表示Boolean类型的数据。

由于null代表空指针，大多数平台中值为0x00，因此null的类型标签就成了0，所以执行typeof时会返回 ‘object’。
% title h2, instanceof运算符 %}

% title h3,判断一个变量是数组还是对象, warning %}


首先我们试着用 typeof 运算符进行测试。

```javascript
	const arr = [0, 1, 2];
	console.log(typeof arr) // "object"
复制代码
```

发现输出结果为"object"，这并不是我们期望的结果。 所以使用 typeof 运算符并不能直接判断一个变量时对象还是数组类型。实际上，typeof 运算符在判断基本数据类型时会很有用，但是在判断引用市局类型时，却显得很吃力。

接下来我们试着用 instanceof 运算符进行测试。

**instance运算符用于通过查找原型链来检测某个变量是否为某个类型数据的实例**。使用instanceof 运算符可以判断一个变量时数组还是对象。

```javascript
        const a = [0, 1, 2];
        console.log(a instanceof Array); // true
        console.log(a instanceof Object); // true

        const b = {name: 'xx'};
        console.log(b instanceof Array); // false
        console.log(b instanceof Object); // true
复制代码
```

有一点需要留意，a 同时还隶属于 Object 类。因为从原型上来讲，Array 是继承自 Object 的。instanceof 在检查中会将原型链考虑在内。 因此我们在判断一个变量时数组还是对象时，应该先判断数组类型，然后再去判断对象类型。如果先判断对象，那么数组值也会被判断为对象类型，这无法满足要求。

我们可以得到以下的封装函数。

```javascript
        function getDataType (o) {
            if (o instanceof Array) return 'Array'
            if (o instanceof Object) return 'Object'
            return 'others'
        }
复制代码
```
{% title h4, 另一种思路：判断构造函数, red %}


判断一个函数是否时数组或者对象，从另一个角度讲，就是判断变量的构造函数时Array类型还是Ojbect类型。因为一个对象的实例都是通过构造函数生成的，所以，我们可以直接判断一个变量的constructor属性。

```javascript
        const a = [0, 1, 2];
        console.log(a.constructor === Array); // true
        console.log(a.constructor === Object);  // false

        const b = {name: 'xx'};
        console.log(b.constructor === Array); // false
        console.log(b.constructor === Object); // true
复制代码
```

我们可以得到以下的封装函数。

```javascript
        function getDataType (o) {
            const constructor = o.__proto__.constructor || o.constructor
            if (constructor === Array) return 'Array';
            if (constructor === Object) return 'Object';
            return 'others'
        }
复制代码
```
{% title h2, Object.prototype.toString 函数, warning %}


{% title h3, 使用 Object.prototype.toString.call() 方法来揭示类型  %}



每种引用类型都会直接或者间接继承自Object类型，因此它们都包含toString()函数。不同数据类型的toString()类型返回值也不一样，所以通过toString()函数可以准确判断值的类型。

- 对于 number 类型，结果是 [object Number]
- 对于 boolean 类型，结果是 [object Boolean]
- 对于 null：[object Null]
- 对于 undefined：[object Undefined]
- 对于数组：[object Array]
- 对于对象：[object Object]
- ……等（可自定义）


{% title h3, Symbol.toStringTag, warning %}
可以使用特殊的对象属性 Symbol.toStringTag 自定义对象的 toString 方法的行为。

```javascript
let user = {
  [Symbol.toStringTag]: "User"
};
alert( {}.toString.call(user) ); // [object User]
复制代码
```

所以，如果我们想要获取内建对象的类型，并希望把该信息以字符串的形式返回，而不只是检查类型的话，我们可以用 {}.toString.call 替代 instanceof。

# 总结

![总结](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9bac10e554c14ea58d2dfdcd96b153d3~tplv-k3u1fbpfcp-zoom-1.image)


