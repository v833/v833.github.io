---
title: 用js代码实现观察者模式
date: 2021-06-11 18:00:52
tags: javascript
swiper: true
swiperDesc: '用js代码实现观察者模式，总结来说观察者模式有两个特点：</br>
发布&订阅</br>
1对N'
categories: 前端
---
{% title h1, 用js代码实现观察者模式 %}
观察者模式定义了对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知，并自动更新。观察者模式属于三种设计类型(创建型、组合型和行为型)中的行为型模式，行为型模式关注的是对象之间的通讯，观察者模式就是观察者和被观察者之间的通讯。

总结来说观察者模式有两个特点：

1.发布&订阅

2.1对N
{% title h1, 观察者模式的现实举例, yellow %}
想象一下，你是一位顶尖歌手，粉丝没日没夜地询问你下个单曲什么时候发。 为了从中解放，你承诺(**发布**)会在单曲发布的第一时间发给他们。你给了粉丝们一个列表。他们可以在上面填写他们的电子邮件地址，以便当歌曲发布后，让所有**订阅**了的人能够立即收到。即便遇到不测，例如录音室发生了火灾，以致你无法发布新歌，他们也能及时收到相关通知。
{% title h1, 用js代码实现观察者模式, warning %}
首先先画一个 UML 简图

![image-20210611001736865.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c67be8ca93ce4e6589afbdf147b881a6~tplv-k3u1fbpfcp-watermark.image)

根据 UML 简图用js代码实现

```js
// 主题，保存状态，状态变化后触发所有观察者对象
class Subject {
  constructor () {
    this.state = 0
    this.observers = []
  }
  getState () {
    return this.state
  }
  setState (state) {
    this.state = state
    this.notifyAllObservers()
  }
  notifyAllObservers () {
    this.observers.forEach(observer => {
      observer.update()
    })
  }
  attach (observer) {
    this.observers.push(observer)
  }
  remove (observer) {
    this.observers = this.observers.filter(sub => sub !== observer)
  }
}
// 观察者
class Observer {
  constructor (name, subject) {
    this.name = name
    this.subject = subject
    this.subject.attach(this)
  }
  update () {
    console.log(`${this.name} update, state: ${this.subject.getState()}`);
  }
}

// 测试
let s = new Subject()
let o1 = new Observer('o1', s)
let o2 = new Observer('o2', s)
let o3 = new Observer('o3', s)
s.setState(1)
s.setState(2)
s.setState(3)
s.remove(o1)
s.setState(4)
复制代码
```
{% title h1, 设计原则, red %}


主题和观察者分离，不是主动触发而是被动监听，两者解耦