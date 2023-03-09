---
title:  Vue中组件通讯
date: 2021-07-6 15:33:32
tags: vue
categories: 前端
---

![组件通讯](https://img10.360buyimg.com/ddimg/jfs/t1/186889/24/12294/65324/60e6a558E458c92c5/4132aa7c6b3e26d1.png)

```
父子组件
A与B、B与C、B与D、C与E、D与E

子孙组件
A与D、B与E

兄弟组件
C与D

隔代组件
A与E

```

## 父、子组件通讯 props、emit、.sync

```
A.vue（父组件）
<template>
    <B data.sync="100“ @eventFn=“parent” />
</template>
<script>
export default {
    setup(props) {
        eventFn = (number) => {}
        return {}
    }
}
</script>

B.vue（子组件）
<template>
    <div>{{ data }}</div>
</template>
<script>
export default {
    props: {
        data: {
            type: String,
            defaule: ""
        }
    },
    setup(){
        emit(“parent”, 100) // 回调父组件的方法
        emit(“update:data”, 111111)
    }
}
</script>
```

## 中央事件总线

```
创建实例
bus.js
import Vue from ‘vue';
export default new Vue();

调用事件
import Bus from 'bus.js'
Bus.$emit('getTarget', 11111);

注册事件
import Bus from 'bus.js'
Bus.$on('getTarget', target => {  
    console.log(target);  
}); 
```

## $attrs、$listeners

![](https://img12.360buyimg.com/ddimg/jfs/t1/196048/5/12137/31100/60e6a558Eb6f30c02/ad8f9abe557abe3f.png)

**注意事件**: 通过props接收相同名称的属性，将不被读取

## provide、inject

**跨级组件间的通信**

祖先组件中通过provider来提供变量，然后在子孙组件中通过inject来注入变量

```
父组件
provide("customVal", "我是父组件向子组件传递的值");

子组件
inject("customVal");
```

## Vuex

```
store
state: { username: 'xx' },
mutation: {
	  SET_USERNAME(state, value) {
      state.username = value
    }
}

index.vue
this.$store.state.commit('SET_USERNAME', 'yy')
```

## $parent、$children、ref