---
title: Vue 的生命周期
date: 2021-06-7 21:05:53
tags: vue
swiper: true
swiperDesc: 'vue 的8个声明周期的总结'
categories: 前端
---
{% title h1, beforeCreate(创建前) %}
{% note success, 在数据观测和初始化事件还未开始 %}

{% title h1, created(创建后) %}
{% note success, 完成数据观测，属性和方法的运算，初始化事件，$el属性还没有显示出来。 %}

{% title h1, beforeMount(载入前) %}
{% note success, 在挂载开始之前被调用，相关的render函数首次被调用。实例已完成以下的配置：编译模板，把data里面的数据和模板生成html。注意此时还没有挂载html到页面上。 %}

{% title h1, mounted（载入后） %}
{% note success, 在el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用。实例已完成以下的配置：用上面编译好的html内容替换el属性指向的DOM对象。完成模板中的html渲染到html页面中。此过程中进行ajax交互。 %}

{% title h1, beforeUpdate（更新前） %}
{% note success, 在数据更新之前调用，发生在虚拟DOM重新渲染和打补丁之前。可以在该钩子中进一步地更改状态，不会触发附加的重渲染过程。 %}

{% title h1, updated（更新后） %}
{% note success, 在由于数据更改导致的虚拟DOM重新渲染和打补丁之后调用。调用时，组件DOM已经更新，所以可以执行依赖于DOM的操作。然而在大多数情况下，应该避免在此期间更改状态，因为这可能会导致更新无限循环。该钩子在服务器端渲染期间不被调用。 %}

{% title h1, beforeDestroy（销毁前） %}
{% note success, 在实例销毁之前调用。实例仍然完全可用。 %}

{% title h1, destroyed（销毁后） %}
{% note success, 在实例销毁之后调用。调用后，所有的事件监听器会被移除，所有的子实例也会被销毁。该钩子在服务器端渲染期间不被调用。 %}


