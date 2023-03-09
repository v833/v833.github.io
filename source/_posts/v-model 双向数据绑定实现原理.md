---
title: v-model 双向数据绑定实现原理
date: 2021-06-1 21:18:57
tags: vue
swiper: true
swiperDesc: 'v-model 时一个语法糖，它做了：
</br>绑定数据value
</br>触发输入事件input
</br>data 更新触发重新渲染
'
categories: 前端
---
{% title h2, 准备工作 %}
我们来手动实现一个 v-model 的功能。


首先定义了一个component组件，里面有一个template。

```js
  const CompOne = {
  props: ['value'],
  template: `
    <div>
      <input type="text"></input>
    </div>
  `,
}
```
然后再定义一个vue对象，里面引用了该组件。

```js
new Vue({
  components: {
    CompOne,
  },
  template: `
    <div>
      <comp-one></comp-one>
    </div> 
  `,
}).$mount(root)
```
好了，准备工作完成。
{% title h2, 实现 %}



![1.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0cc2c223924940259fa124c63a96c3e4~tplv-k3u1fbpfcp-watermark.image)

```js
const CompOne = {
  props: ['value'], // 接收父组件传递的数据
  template: `
    <div>
      <input type="text" @input="handleInput" :value="value"></input> 
      // 添加一个input事件
      // 接收父组件传递的value
    </div>
  `,
  methods: {
    handleInput (e) {
      this.$emit('input', e.target.value) // 通过$emit，当 value 变化时通知父组件
    }
  }
}
```

![2.gif](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af3420cbfa4e4a818b203157ce8e3d7a~tplv-k3u1fbpfcp-watermark.image)

```js
new Vue({
  components: {
    CompOne,
  },
  data () {
    return {
      value: 'vue' 
    }
  },
  template: `
    <div>
    // value 为传递给子组件的数据
    // @input 接收子组件通过 $emit 传递的参数
      <comp-one :value="value" @input="value = arguments[0]"></comp-one> 
    </div> 
  `,
}).$mount(root)
```

{% title h2, 效果 %}
![3.gif](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1428aadc50644b9ad1629983bea1780~tplv-k3u1fbpfcp-watermark.image)

大功告成，我们实现了 v-model 的功能！
{% title h2, 拓展 %}


 默认情况下，一个组件上的 v-model 会把 value 用作 prop 且把 input 用作 event。

 但是像单选框、复选框等类型的输入控件可能会将 value attribute 用于不同的目的。model 选项可以用
 来避免这样的冲突。
 ```js
new Vue({
  components: {
    CompOne,
  },
  data () {
    return {
      value: 'vue'
    }
  },
  template: `
    <div>
      <comp-one v-model="value"></comp-one> // 使用v-model 满足语法糖规则：属性必须为value，方法名必须为：input
    </div> 
  `,
}).$mount(root)
 ```
```js
// 修改子组件 
const CompOne = {
  props: ['value1'],
  model: {
      prop: "value1", // 接收的数据 value => value1
      event: "change" // $emit 需要绑定的事件 input => change
  },
  template: `
    <div>
      <input type="text" @input="handleInput" :value="value1"></input>
    </div>
  `,
  methods: {
    handleInput (e) {
      this.$emit('change', e.target.value)
    }
  }
}

```
这样就可以让 v-model 变得更灵活，属性和方法名可以自定义。

{% title h2, 总结 %}

v-model 时一个语法糖，它做了：

1. 绑定数据value

2. 触发输入事件input

3. data 更新触发重新渲染