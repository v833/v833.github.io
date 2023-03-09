---
title: 学习 Vue 原理：响应式
date: 2021-05-30 19:08:31
tags: vue
swiper: true
swiperDesc: '
</br>什么是响应式？
</br>数据驱动视图
</br>如何实现响应式
'
categories: 前端
---

{% note info, 近来在学习Vue，对于它的核心概念之一——响应式一直有所困惑，偶然间发现一门课程vue advanced workshop with Evan You。Vue的作者尤雨溪亲自讲解Vue。下面是对该课程学习的总结。欢迎大家参考和提出意见。 %}


{% title h2, 什么是响应式？ %}
响应式是 Vue的一个核心特性，用于监听视图中绑定的数据，当数据发生改变时视图自动更新。

只要状态发生改变，系统依赖部分发生自动更新就可以称为响应性。

在web的场景下，就是不断变化的状态反应到DOM上的变化。

响应式实现数据驱动视图的第一步。

{% title h2, 数据驱动视图 %}
现在有这样一个例子：

> 变量 a 和变量 b ，变量 b 的值永远等于 a 的 10 倍。

如果使用命令式编程，可以很简单实现。

```javascript
let a = 3;
let b = a * 10;
console.log(b) // 30
```
但是当我们设置 b 的值为 4 时， b 还是等于 30

```javascript
let a = 3;
let b = a * 10;
console.log(b) // 30
a = 4; // 命令式，b不会保持关系同步
console.log(b) // 30
```
那么该如何实现当a改变时，b同时也改变呢？

> 这里有一个神奇的函数onChanged()，它接收一个函数并且当 a 的值改变时，可以自动执行里面的代码，我们将 b 的更新放在里面，问题就解决了。
```javascript
onChanged (() => b = a * 10 ) // 声明式, b 随着 a 改变而改变
```
我们扩展一下，下面代码同样有一个神奇函数onStateChange，它会在 state 改变的时候自动运行，那我们只要在函数中编写dom操作的代码，就可以实现 dom 的自动更新了。

```javascript
// DOM元素
<span class="cell b1"></span>

// 神奇函数，当state值改变会自动重新运行
onStateChange(() => {
  document.querySelector('.cell.b1').textContent = state.a * 10
})
```
我们再进一步抽象，把 dom 的操作使用渲染引擎替换，但是我们不去研究渲染引擎的实现，只是简单的认为它会自动解析模版代码与数据关联即可，那代码就会变成下面这样。

```javascript
// DOM元素
<span class="cell b1">
	{{ state.a * 10 }}
</span>

// 神奇函数，当state值改变会自动重新运行
onStateChange(() => { view = render(state) })
```

{% title h2, 如何实现响应式 %}

{% title h3, getter 和 setter, warning %}
Vue 中对象会被转换成响应式， 使用ES5的 defineProperty() 重写所有属性的 getter 和 setter 方法。

[MDN上关于Object.defineProperty的介绍](https://developer.mozilla.org/zh-CN/docs/web/javascript/reference/global_objects/object/defineproperty)

下面将演示如何通过convert函数修改传入对象的getter和setter实现修改对象属性

```javascript

function convert(obj) {
	Object.keys(obj).forEach(key => {
		let internalValue = obj[key] // 闭包，提供了存储机会 内部值
		Object.defineProperty(obj, key, {
			get () {
				return internalValue
			},
			set (newValue) {
				internalValue = newValue
			}
		})
	})
}
```

{% title h3, 依赖跟踪（订阅发布模式）, warning %}

{% title h4, 为什么要依赖收集？, red %}
先来看下面的代码

```new Vue({
    template: 
        `<div>
            <span>text1:</span> {{text1}}
        <div>`,
    data: {
        text1: 'text1',
        text2: 'text2',
    }
});
```
按照之前响应式中的方法进行绑定则会出现一个问题——text3在实际模板中并没有被用到，然而当text3的数据被修改（this.text3 = 'test'）的时候，同样会触发text3的setter导致重新执行渲染，这显然不正确。

所以我们要进行**依赖收集**。
{% title h4, 如何实现？, red %}
**创建一个依赖跟踪类Dep，里面有两个方法：'depend' 'notify'。**

'depend': 收集这种依赖项。

'notify': 表示依赖发生改变，任何之前被定义为依赖的表达式、函数、计算都会被通知重新执行。也就是说我们需要找到一种让他们建立关联的方法。我们把这种计算关系叫依赖。这种计算也被认为是订阅者模式。
下面是Dep类期望达到的效果，调用dep.depend方法收集收集依赖，当调用dep.notify方法，控制台会再次输出updated语句。

```javascript
const dep = new Dep()

autorun (() => {
	dep.depend() // 实际上是把这个函数添加到订阅者列表中dep中,之后无论你在任何地方调用
	console.log("updated")
})
// should log: "updated"

dep.notify()
// 函数再次被调用 should log: "updated" autorun
```
这个 autorun 函数接收一个更新函数或者表达式，当你进入这个更新函数时，一切都变得特别，当代码放在这个响应区内，就可以通过dep.depend 方法注册依赖项。

代码实现

```javascript
window.Dep = class Dep {
	constructor () {
		this.subsctibers = new Set()
	}
	depend () {
		if (activeUpdate) {
			// 注册这个activeUpdate作为订阅者
			this.subscribers.add(activeUpdate)
		}
	},
	notify () {
		// 通知所有订阅者
		this.subscribers.forEach(sub => sub()) // 获取订阅函数并执行
	}
}
let activeUpdate // 发布者

function autorun (update) {
	function wrappedUpdate () {
		activeUpdate = wrappedUpdate // 赋值给 wrappedUpdate 会使得当依赖关系发生改变update 函数会重新执行 动态更新依赖 保证依赖一直是最新的
		update()
		wrappedUpdate = null
	}
	updateWrapper();
}

autorun(() => {
	dep.depend()
})
```
{% title h3, 实现迷你响应性系统, warning %}
结合前面两个函数convert() autorun() 将covert改名成observe()。
observer需要一个监听对象，监听他们得getter和setter，在getters和setters里面，我们可以设置依赖。
都整合后我们相当于创建了一个对象，我们访问一个属性，它收集依赖，调用dep.depend 当我们通过赋值改变属性值，调用notify触发改变。


期望实现的调用效果：

```javascript
const state = {
	count: 0
}

observe(state)

autorun(() => console.log(state.count))
// should immediately log "count is: 0"

state.count ++
// should log "count is: 1"
```
最终整合代码如下：

```javascript
class Dep {
  constructor () {
    this.subscribers = new Set()
  }

  depend () {
    if (activeUpdate) {
      this.subscribers.add(activeUpdate)
    }
  }

  notify () {
    this.subscribers.forEach(sub => sub())
  }
}

function observe (obj) {
  Object.keys(obj).forEach(key => {
    let internalValue = obj[key]

    const dep = new Dep()
    Object.defineProperty(obj, key, {
      // 在getter收集依赖项，当触发notify时重新运行
      get () {
        dep.depend()
        return internalValue
      },

      // setter用于调用notify
      set (newVal) {
        if (internalValue !== newVal) {
          internalValue = newVal
          dep.notify()
        }
      }
    })
  })
  return obj
}

let activeUpdate = null

function autorun (update) {
  const wrappedUpdate = () => {
    activeUpdate = wrappedUpdate
    update()
    activeUpdate = null
  }
  wrappedUpdate()
}
```
以上都是基于个人的理解，写的学习笔记，欢迎大家提出建议。