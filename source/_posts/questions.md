---
title: questions(持续更新)
date: 2021-06-5 19:42:32
tags: 日常记录
categories: 学习笔记
---
##### 1.var 和 let const 的区别？

使用var声明的变量，其作用域为该语句所在的函数内，且存在变量提升现象；
		使用let const声明的变量，其作用域为该语句所在的代码块内，不存在变量提升；
		使用const声明的是常量，在后面出现的代码中不能再修改该常量的值。

let声明的全局变量不是全局对象的属性

let声明的变量拥有块级作用域。

用let重定义变量会抛出一个语法错误（SyntaxError）。

形如for (let x...)的循环在每次迭代时都为x创建新的绑定。

声明会被提升，但是赋值不会。

##### 2.typeof能返回哪些类型？

numer string undefinend boolean symbol object function

##### 3、列举强制类型转换和隐式类型转换

强制类型转换：parseInt parseFloat toString

隐式类型转换： if、逻辑运算、==、+拼接字符串

##### 4、手写深度比较，模拟lodash isEqual

```
		function isObject(obj) {
            return typeof obj === 'object' && obj !== null
        }

        function isEqual(a, b) {
            if (!isObject(a) || !isObject(b)) return a === b // 是否是对象
            if (a === b) return true // 是否是同一个引用
            if (Object.keys(a).length !== Object.keys(b).length) return false
            for (let k in a) {
                const res = isEqual(a[k], b[k]);
                if (!res) return false

            }

            return true
        }
```



##### 5、split()和join()区别

split() 将字符串分解成数组

使用指定的分隔符字符串将一个[`String`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String)对象分割成子字符串数组，以一个指定的分割字串来决定每个拆分的位置。 

split()只有一个参数时：分割的字符串或正则表达式；两个参数时，第二个参数是指返回的数组中元素的个数；

join() 将数组分解成字符串

用于连接多个字符或字符串，返回值为一个字符串 默认分割号为逗号

**String.split() 执行的操作与 Array.join() 执行的操作是相反的。**

##### 6、pop push unshift shift 返回值？

`push()` 和 `unshift()` 会返回新数组的长度，而 `pop()` 和 `shift()` 会返回被移除的元素（当数组为空时返回 `undefined`）

##### 7、数组的API有哪些是纯函数？

纯函数：1.不改变原数组  2.返回一个数组

arr.slice()arr.map()arr.forEach()arr.every()arr.some()arr.filter()arr.reduce()arr.entries()arr.find()

arr.concat

会改变原数组的方法：arr.splice()
		arr.reverse()arr.fill()arr.copyWithin()arr.sort()arr.push()arr.pop()arr.unshift()arr.shift()

##### 8、数组slice和splice的区别？

slice切片 splice剪切

slice() 返回一个数组，不改原数组 

splice() 返回一个数组，改变原数组

##### 9、[10,20,30].map(parseInt)返回值？

[10, NaN, NaN]

##### 10、ajax请求get和post的区别

get请求一般用于查询，post一般用于用户提交

get参数拼接再url上，post放在请求体内(数据体积可更大)

##### 11、函数call和apply bind区别？

它们的共同点是，都能够**改变函数执行时的上下文**，将一个对象的方法交给另一个对象来执行，并且是立即执行的。

区别在参数上，call是零散传入，apply是传入一个数组或者类数组

**调用 call 和 apply 的对象，必须是一个函数 Function**

**类数组无法使用 forEach、splice、push 等数组原型链上的方法**

**bind 方法的返回值是函数，并且需要稍后调用，才会执行**。而 apply 和 call 则是立即调用。

```
function add (a, b) {
    return a + b;
}

function sub (a, b) {
    return a - b;
}

add.bind(sub, 5, 3); // 这时，并不会返回 8
add.bind(sub, 5, 3)(); // 调用后，返回 8
```

##### 12、事件代理(委托)是什么？

原本需要绑定在子元素的响应事件委托给父元素，让父元素担当事件监听的职务。 事件代理的原理是DOM元素的事件冒泡。

##### 13、闭包是什么，有什么特性？有什么负面影响？

函数可以记住并访问所在的词法作用域 。

父函数先于子函数销毁，子函数需要用到的变量保存在[[scoped]]中

影响：变量会常驻内存(**可达性**)，得不到释放。闭包不要乱用。

函数作为返回值--内存不会释放

##### 14、如何阻止事件冒泡和默认行为？

e.prventDefault()

e.stopPropagation()

##### 15、查找、添加、删除、移动DOM节点的方法？

appendChild() removeChild() insertBefore() replaceChild()

##### 16、如何减少DOM操作？

缓存DOM查询结果

多次DOM操作，合并到一次插入

document.createDocumentFragment() 创建一个文档片段

##### 17、解释jsonp的原理，为什么它不是真正的ajax？

浏览器的同源策略（**服务器端没有同源策略**）和跨域

哪些html标签可以跨域？ img script css

利用< script>标签可以跨域，让服务器端返回可执行的Javascript函数，参数为要回发的数据。

##### 18、document.load 和 ready 的区别？

load 网页全部资源加载完之后执行 

ready DOM加载完执行

##### 19、== 和 === 的区别？

=== 严格相等 == 会进行类型转换

##### 20、函数声明和函数表达式的区别？

函数声明会被提升，函数表达式不会提升

##### 21、new Object() 和 Object.create()的区别？

字面量和`new`关键字创建的对象是`Object`的实例，原型指向`Object.prototype`，继承内置对象`Object`

`Object.create(arg, pro)`创建的对象的原型取决于`arg`，`arg`为`null`，新对象是空对象，没有原型，不继承任何对象；`arg`为指定对象，新对象的原型指向指定对象，继承指定对象

##### 22、关于this的场景题？

由new调用？绑定到新创建的对象

由call(apply, bind)调用？绑定到指定的对象

由上下文对象调用？绑定到那个上下文

默认：严格模式绑定到undefined 否则绑定到全局对象

##### 23、`setInterval` 方法的返回值是什么？

```
setInterval(fn, delay, arg1, arg2...)
```

`setInterval` 返回一个唯一的 id。此 id 可被用于 `clearInterval` 函数来取消定时。

##### 24、输出是什么?

```
const name = "Lydia";
age = 21;

console.log(delete name);
console.log(delete age);
```

`delete`操作符返回一个布尔值： `true`指删除成功，否则返回`false`. 但是通过 `var`, `const` 或 `let` 关键字声明的变量无法用 `delete` 操作符来删除。

`name`变量由`const`关键字声明，所以删除不成功:返回 `false`. 而我们设定`age`等于`21`时,我们实际上添加了一个名为`age`的属性给全局对象。对象中的属性是可以删除的，全局对象也是如此，所以`delete age`返回`true`.

##### 25、输出是什么?

```
const value = { number: 10 };

const multiply = (x = { ...value }) => {
  console.log(x.number *= 2);
};

multiply(); 20
multiply(); 20
multiply(value); 20
multiply(value); 40
```

在ES6中，我们可以使用默认值初始化参数。如果没有给函数传参，或者传的参值为 `"undefined"` ，那么参数的值将是默认值。上述例子中，我们将 `value` 对象进行了解构并传到一个新对象中，因此 `x` 的默认值为 `{number：10}` 。

默认参数在调用时才会进行计算，每次调用函数时，都会创建一个新的对象。我们前两次调用 `multiply` 函数且不传递值，那么每一次 `x` 的默认值都为 `{number：10}` ，因此打印出该数字的乘积值为`20`。

第三次调用 `multiply` 时，我们传递了一个参数，即对象`value`。 `*=`运算符实际上是`x.number = x.number * 2`的简写，我们修改了`x.number`的值，并打印出值`20`。

第四次，我们再次传递`value`对象。 `x.number`之前被修改为`20`，所以`x.number * = 2`打印为`40`

##### 26、输出什么?

```
[1, 2, 3, 4].reduce((x, y) => console.log(x, y));
```

```
1` `2` and `undefined` `3` and `undefined` `4
```

`reducer` 函数接收4个参数:

1. Accumulator (acc) (累计器)
2. Current Value (cur) (当前值)
3. Current Index (idx) (当前索引)
4. Source Array (src) (源数组)

`reducer` 函数的返回值将会分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

`reducer` 函数还有一个可选参数`initialValue`, 该参数将作为第一次调用回调函数时的第一个参数的值。如果没有提供`initialValue`，则将使用数组中的第一个元素。

在上述例子，`reduce`方法接收的第一个参数(Accumulator)是`x`, 第二个参数(Current Value)是`y`。

在第一次调用时，累加器`x`为`1`，当前值`“y”`为`2`，打印出累加器和当前值：`1`和`2`。

例子中我们的回调函数没有返回任何值，只是打印累加器的值和当前值。如果函数没有返回值，则默认返回`undefined`。 在下一次调用时，累加器为`undefined`，当前值为“3”, 因此`undefined`和`3`被打印出。

在第四次调用时，回调函数依然没有返回值。 累加器再次为 `undefined` ，当前值为“4”。 `undefined`和`4`被打印出。

##### 27、判断字符串以字母开头，后面字母数字下划线，长度6-30？

**js把以0开头的数字转换成字符串时，当成8进制**

regExp检测的是**字符串**

##### 28、手写字符串trim方法，保证浏览器兼容性

```
	String.prototype.trim = function () {
            return this.replace(/^\s+/, '').replace(/\s+$/, '')
        }
```

##### 29、如何获取多个数字中的最大值

Math.max()

##### 30、如何用JS实现继承？

class继承

prototype继承

##### 31、将类数组变为数组？

Array.from()

Array.prototye.slice.call(arguments) 

[].slice.call(arguments)

[...args]

##### 32、如何捕获JS程序中的异常？

```
		try {

        } catch (ex) {
            console.error(ex);
        } finally {
            
        }
        
        window.onerror = function(message, source, lineNum, colNum, error)
```

##### 33、什么是JSON？

json是一种数据格式，本质是一段**字符串**

json格式和JS对象结构一致，对JS语言更友好

##### 34、获取当前页面url参数？

location.search

```
		function query(name) {
            let search = location.search
            const p = new URLSearchParams(search)
            return p.get(name)
        }
```

##### 35、输出什么？

```
const { name: myName } = { name: "Lydia" };

console.log(name);
```

当我们从右侧的对象解构属性`name`时，我们将其值`Lydia`分配给名为`myName`的变量。

使用`{name：myName}`，我们是在告诉JavaScript我们要创建一个名为`myName`的新变量，并且其值是右侧对象的`name`属性的值。

当我们尝试打印`name`，一个未定义的变量时，就会引发`ReferenceError`。

##### 36、什么是纯函数？

纯函数在相同的输入值时，需产生相同的输出，纯函数不会产生副作用。

##### 37、输出什么？

```
function checkAge(age) {
  if (age < 18) {
    const message = "Sorry, you're too young."
  } else {
    const message = "Yay! You're old enough!"
  }

  return message
}

console.log(checkAge(21))
```

`const`和`let`声明的变量是具有**块级作用域**的，块是大括号（`{}`）之间的任何东西, 即上述情况`if / else`语句的花括号。 由于块级作用域，我们无法在声明的块之外引用变量，因此抛出`ReferenceError`。

##### 38、将url参数解析为js对象？

##### 39、手写数组flatern，考虑多层级

```
[].concat(arr) // 一层flat
			function flat (arr) {
            const isDeep = arr.some(item => item instanceof Array)
            if (!isDeep) return arr
            const res = [].concat.apply([], arr)
            return flat(res)
        } // 多层级
```

##### 40、数组去重

```
		function unique(arr) {
            const res = []
            arr.forEach(item => {
                if (res.indexOf(item) < 0) {
                    res.push(item)
                }       
            });

            return res
        }
        
        Array.from(new Set(arr))
        [...new Set(arr)]
```

##### 41、介绍一下RAF window.requestAnimationFrame

想要动画流畅，更新频率要60帧/s，即16.67ms更新一次视图

setTimeout要手动控制频率，而RAF浏览器会自动控制

后台标签或隐藏iframe,Raf会暂停，而timeout依然运行

##### 42、前端性能优化?一般从哪些方面考虑？

原则：多使用内存、缓存，减少计算，减少网络请求

方向：加载页面，页面渲染，页面操作流畅度



##### 43、

```
<div id="app">
    <span class=span-a>
      {{obj.a}} 
    </span>
    <span class=span-b>
      {{obj.b}}
    </span>
  </div>
  
  // js
  var app = new Vue({
  el: '#app',
  data: {
    obj: {
      a: 'a',
    }
  },
})
app.obj.a = 'a2'
app.obj.b = 'b'
```

**深入分析：**

- 要理解为什么 span-b 会更新，要点是理解视图更新其实是**异步**的。
- 当我们让 a 从 'a1' 变成 'a2' 时，Vue 会监听到这个变化，但是 Vue 并不能马上更新视图，因为 Vue 是使用 Object.defineProperty 这样的方式来监听变化的，监听到变化后会创建一个视图更新任务到任务队列里。
- 所以在视图更新之前，要先把余下的代码运行完才行，也就是会运行 b = 'b'。
- 等到视图更新的时候，由于 Vue 会去做 diff（文档中有写），于是 Vue 就会发现 a 和 b 都变了，自然会去更新 span-a 和 span-b。

##### 44、如何理解暂时性死区？

从程序执行进入代码块（或函数）的那一刻起，变量就开始进入“未初始化”状态。它一直保持未初始化状态，直至程序执行到相应的 `let` 语句。

换句话说，一个变量从技术的角度来讲是存在的，但是在 `let` 之前还不能使用。

引擎从函数开始就知道局部变量 x，  但是变量 x 一直处于“未初始化”（无法使用）的状态，直到结束 let（“死区”） 

##### 45、下面这段代码的结果会是什么？

```
let x = 1;

function func() {
  console.log(x); // ?

  let x = 2;
}

func(); //error
```

1. 当脚本开始运行，词法环境预先填充了所有声明的变量。
   - 最初，它们处于“未初始化（Uninitialized）”状态。这是一种特殊的内部状态，这意味着引擎知道变量，但是在用 `let` 声明前，不能引用它。几乎就像变量不存在一样。
2. 然后 `let phrase` 定义出现了。它尚未被赋值，因此它的值为 `undefined`。从这一刻起，我们就可以使用变量了。

##### 46、href url src ?

href 目的不是为了引用资源，而是为了建立联系，让当前标签能够链接到目标地址。

source（缩写），指向外部资源的位置，指向的内容将会应用到文档中当前标签所在位置。

在请求 src 资源时会将其指向的资源下载并应用到文档中，比如 JavaScript 脚本，img 图片；

（1）href 用于在当前文档和引用资源之间确立联系；

（2）src 用于替换当前内容；

##### 47、token?

这里说一下token，一般是在登录完成之后，将用户的token通过localStorage或者cookie存在本地，然后用户每次在进入页面的时候（即在main.js中），会首先从本地存储中读取token，如果token存在说明用户已经登陆过，则更新vuex中的token状态。然后，在每次请求接口的时候，都会在请求的header中携带token，后台人员就可以根据你携带的token来判断你的登录是否过期，如果没有携带，则说明没有登录过。这时候或许有些小伙伴会有疑问了，就是每个请求都携带token，那么要是一个页面不需要用户登录就可以访问的怎么办呢？其实，你前端的请求可以携带token，但是后台可以选择不接收啊！

##### 48、重绘和重排(回流)？

当盒子的位置、大小以及其他属性，例如颜色、字体大小等都确定下来之后，浏览器便把这些原色都按照各自的特性绘制一遍，将内容呈现在页面上。**重绘是指一个元素外观的改变**所触发的浏览器行为，浏览器会根据元素的新属性重新绘制，使元素呈现新的外观。 

**触发重绘的条件：改变元素外观属性。如：color，background-color等。**

**注意：**table及其内部元素可能需要多次计算才能确定好其在渲染树中节点的属性值，比同等元素要多花两倍时间，这就是我们尽量避免使用table布局页面的原因之一。

**重排（重构/回流/reflow）：**当渲染树中的一部分(或全部)因为元素的**规模尺寸，布局，隐藏等改变而需要重新构建, 这就称为回流(reflow)**。每个页面至少需要一次回流，就是在页面第一次加载的时候。

**重绘和重排的关系**：在回流的时候，浏览器会使渲染树中受到影响的部分失效，并重新构造这部分渲染树，完成回流后，浏览器会重新绘制受影响的部分到屏幕中，该过程称为重绘。

我们要减少重绘和重排就是要减少对**渲染树**的操作

**将需要多次重排的元素，position属性设为absolute或fixed**，元素脱离了文档流，它的变化不会影响到其他元素

##### 49、页面渲染过程？

解析 => 布局=> 绘制=> 光栅化

##### 50、对async、await的理解，内部原理？

`async`函数返回一个 Promise 对象，可以使用`then`方法添加回调函数。当函数执行的时候，一旦遇到`await`就会先返回，等到异步操作完成，再接着执行函数体内后面的语句。

##### 51、flex

**flex-grow ：**flex-grow属性定义盒子的**放大**比例，默认为0 不放大  其他数字按比例放大

**flex-shrink：**如果所有项目的flex-shrink属性都为1，当空间不足时，都将**等比例缩小**。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者**0的不缩小 其他缩小**

##### 52、cookie?

cookie 做什么? 一般用来存储数据  比如 用户的登录状态  不过现在经常用token 和localStorage了

**HTTP协议本身是无状态的**。什么是无状态呢，即服务器无法判断用户身份。Cookie实际上是一小段的文本信息（key-value格式）。客户端向服务器发起请求，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。**服务器检查该Cookie，以此来辨认用户等状态**。

##### 53、输出是什么?

```
const person = { name: "Lydia" };

Object.defineProperty(person, "age", { value: 21 });

console.log(person);
console.log(Object.keys(person));
```

- A: `{ name: "Lydia", age: 21 }`, `["name", "age"]`

- B: `{ name: "Lydia", age: 21 }`, `["name"]`

- C: `{ name: "Lydia"}`, `["name", "age"]`

- D: `{ name: "Lydia"}`, `["age"]`

- 答案: B

  通过`defineProperty`方法，我们可以给对象添加一个新属性，或者修改已经存在的属性。而我们使用`defineProperty`方法给对象添加了一个属性之后，属性默认为 *不可枚举(not enumerable)*. `Object.keys`方法仅返回对象中 *可枚举(enumerable)* 的属性，因此只剩下了`"name"`.

  用`defineProperty`方法添加的属性默认不可变。你可以通过`writable`, `configurable` 和 `enumerable`属性来改变这一行为。这样的话， 相比于自己添加的属性，`defineProperty`方法添加的属性有了更多的控制权。

##### 54、输出什么?

```
[1, 2, 3, 4].reduce((x, y) => console.log(x, y));
```

- A: `1` `2` and `3` `3` and `6` `4`
- B: `1` `2` and `2` `3` and `3` `4`
- C: `1` `undefined` and `2` `undefined` and `3` `undefined` and `4` `undefined`
- D: `1` `2` and `undefined` `3` and `undefined` `4`

答案: **D**

`reducer` 函数接收4个参数:

1. Accumulator (acc) (累计器)
2. Current Value (cur) (当前值)
3. Current Index (idx) (当前索引)
4. Source Array (src) (源数组)

`reducer` 函数的返回值将会分配给累计器，该返回值在数组的每个迭代中被记住，并最后成为最终的单个结果值。

`reducer` 函数还有一个可选参数`initialValue`, 该参数将作为第一次调用回调函数时的第一个参数的值。如果没有提供`initialValue`，则将使用数组中的第一个元素。

在上述例子，`reduce`方法接收的第一个参数(Accumulator)是`x`, 第二个参数(Current Value)是`y`。

在第一次调用时，累加器`x`为`1`，当前值`“y”`为`2`，打印出累加器和当前值：`1`和`2`。

例子中我们的回调函数没有返回任何值，只是打印累加器的值和当前值。如果函数没有返回值，则默认返回`undefined`。 在下一次调用时，累加器为`undefined`，当前值为“3”, 因此`undefined`和`3`被打印出。

在第四次调用时，回调函数依然没有返回值。 累加器再次为 `undefined` ，当前值为“4”。 `undefined`和`4`被打印出。

##### 55、输出什么?

```
// index.js
console.log('running index.js');
import { sum } from './sum.js';
console.log(sum(1, 2));

// sum.js
console.log('running sum.js');
export const sum = (a, b) => a + b;
```

- A: `running index.js`, `running sum.js`, `3`

- B: `running sum.js`, `running index.js`, `3`

- C: `running sum.js`, `3`, `running index.js`

- D: `running index.js`, `undefined`, `running sum.js`

- **答案: B**

  `import`命令是编译阶段执行的，在代码运行之前。因此这意味着被导入的模块会先运行，而导入模块的文件会后执行。

  这是CommonJS中`require（）`和`import`之间的区别。使用`require()`，您可以在运行代码时根据需要加载依赖项。 如果我们使用`require`而不是`import`，`running index.js`，`running sum.js`，`3`会被依次打印。

##### 56、输出什么?

```
function addToList(item, list) {
  return list.push(item);
}

const result = addToList("apple", ["banana"]);
console.log(result);
```

- A: `['apple', 'banana']`

- B: `2`

- C: `true`

- D: `undefined`

- **答案: B**

  `push()`方法返回新数组的长度。一开始，数组包含一个元素（字符串`"banana"`），长度为1。 在数组中添加字符串`"apple"`后，长度变为2，并将从`addToList`函数返回。

  `push`方法修改原始数组，如果你想从函数返回数组而不是数组长度，那么应该在push `item`之后返回`list`。

##### 57、输出什么?

```
function giveLydiaPizza() {
  return "Here is pizza!"
}

const giveLydiaChocolate = () => "Here's chocolate... now go hit the gym already."

console.log(giveLydiaPizza.prototype)
console.log(giveLydiaChocolate.prototype)
```

- A: `{ constructor: ...}` `{ constructor: ...}`

- B: `{}` `{ constructor: ...}`

- C: `{ constructor: ...}` `{}`

- D: `{ constructor: ...}` `undefined`

- **答案: D**

  常规函数，例如`giveLydiaPizza`函数，有一个`prototype`属性，它是一个带有`constructor`属性的对象（原型对象）。 然而，箭头函数，例如`giveLydiaChocolate`函数，没有这个`prototype`属性。 尝试使用`giveLydiaChocolate.prototype`访问`prototype`属性时会返回`undefined`。

##### 58、`... args`是剩余参数，剩余参数的值是一个包含所有剩余参数的数组，**并且只能作为最后一个参数**。上述示例中，剩余参数是第二个参数，这是不可能的，并会抛出语法错误。

##### 59、输出什么?

```
const getList = ([x, ...y]) => [x, y]
const getUser = user => { name: user.name, age: user.age }

const list = [1, 2, 3, 4]
const user = { name: "Lydia", age: 21 }

console.log(getList(list))
console.log(getUser(user))
```

- A: `[1, [2, 3, 4]]` and `undefined`

- B: `[1, [2, 3, 4]]` and `{ name: "Lydia", age: 21 }`

- C: `[1, 2, 3, 4]` and `{ name: "Lydia", age: 21 }`

- D: `Error` and `{ name: "Lydia", age: 21 }`

- **答案: A**

  `getList`函数接收一个数组作为其参数。 在`getList`函数的括号之间，我们立即解构这个数组。 您可以将其视为：

  ```
  [x, ...y] = [1, 2, 3, 4]
  ```

  使用剩余的参数`... y`，我们将所有剩余参数放在一个数组中。 在这种情况下，其余的参数是`2`，`3`和`4`。 `y`的值是一个数组，包含所有其余参数。 在这种情况下，`x`的值等于`1`，所以当我们打印`[x，y]`时，会打印`[1，[2,3,4]]`。

  `getUser`函数接收一个对象。对于箭头函数，如果只返回一个值，我们不必编写花括号。但是，如果您想从一个箭头函数返回一个对象，您必须在圆括号之间编写它，否则不会返回任何值!下面的函数将返回一个对象:

  ```
  const getUser = user => ({ name: user.name, age: user.age })
  ```

  由于在这种情况下不返回任何值，因此该函数返回`undefined`。

##### 60、输出什么?

```
const output = `${[] && 'Im'}possible!
You should${'' && `n't`} see a therapist after so much JavaScript lol`
```

- A: `possible! You should see a therapist after so much JavaScript lol`

- B: `Impossible! You should see a therapist after so much JavaScript lol`

- C: `possible! You shouldn't see a therapist after so much JavaScript lol`

- D: `Impossible! You shouldn't see a therapist after so much JavaScript lol`

- **答案: B**

  `[]`是一个真值。 使用`&&`运算符，如果左侧值是真值，则返回右侧值。 在这种情况下，左侧值`[]`是一个真值，所以返回`Im`。

  `""`是一个假值。 如果左侧值是假的，则不返回任何内容。 `n't`不会被退回。

##### 61、结果是什么?

``` 
Promise.resolve(5) // Promise {<fulfilled>: 5}
=== let promise = new Promise(resolve => resolve(5)) 一个reloved的promise
```

##### 62、输出什么？

```
function* generatorOne() {
  yield ['a', 'b', 'c'];
}

function* generatorTwo() {
  yield* ['a', 'b', 'c'];
}

const one = generatorOne()
const two = generatorTwo()

console.log(one.next().value)
```

- A: `a` and `a`
- B: `a` and `undefined`
- C: `['a', 'b', 'c']` and `a`
- D: `a` and `['a', 'b', 'c']`
- 在函数 `generatorTwo` 中, 我们使用 `yield*` 关键字。就相当于函数`two`第一个`yield`的值, 等价于在迭代器中第一个 `yield` 的值。数组`['a', 'b', 'c']`就是这个迭代器. 第一个 `yield` 的值就是 `a`, 所以我们第一次调用 `two.next().value`时, 就返回`a`。

##### 63、将会发生什么?

```
let config = {
  alert: setInterval(() => {
    console.log('Alert!')
  }, 1000)
}

config = null
```

- A: `setInterval` 的回调不会被调用
- B: `setInterval` 的回调被调用一次
- C: `setInterval` 的回调仍然会被每秒钟调用
- D: 我们从没调用过 `config.alert()`, config 为 `null`

**答案: C**

一般情况下当我们将对象赋值为 `null`, 那些对象会被进行 *垃圾回收（garbage collected）* 因为已经没有对这些对象的引用了。然而，`setInterval`的参数是一个箭头函数（所以上下文绑定到对象 `config` 了），回调函数仍然保留着对 `config`的引用。只要存在引用，对象就不会被垃圾回收。因为没有被垃圾回收，`setInterval` 的回调每1000ms (1s)会被调用一次。
##### 64、Event Bus不方便之处
vue2里面可以用new Vue() 创建对象，调用对象的$enit和$on，在vue3里$on被废弃，只能自己实现或者用第三方的事件管理器(cookie, localStorage, sessionStorage, vuex)

在想拿到数据之前需要绑定事件监听，在发送数据时需要触发，很麻烦

对组件的生命周期也会对事件传递产生影响。比如一个组件在触发事件时，另外一个本需要该数据的组件可能尚未初始化未设置监听
##### 65、vuex和localStorage区别
localStrage 会让数据永久保存在本地，Vuex只是让当前页面不同组件间能共享数据

页面刷新时，vuex数据初始化(菜单导航不能把控制变量放在vuex)

localStorage没有响应式，一个组件对localStorage数据的修改不能让另外一个使用该数据的组件视图重新渲染

vuex能规范不同组件对全局状态的修改