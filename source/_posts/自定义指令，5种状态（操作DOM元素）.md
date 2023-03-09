---
title:  自定义指令，5种状态（操作DOM元素）
date: 2021-07-08 15:33:32
tags: vue
categories: 前端
---

```
Vue.directive("hello",{
  bind:function(el,bingind,vnode){
  	  bingind.value
  	  bingind.def
      el.style["color"] = bingind.value;
      console.log("1-bind");
  },
  inserted:function(){
      console.log("2-insert");
  },
  update:function(){
      console.log("3-update");
  },
  componentUpdated:function(){
      console.log('4 - componentUpdated');
  },
  unbind:function(){
      console.log('5 - unbind');
  }
})
```

##### bind

 只调用一次，指令第一次绑定到元素时候调用，用这个钩子可以定义一个绑定时执行一次的初始化动作。

##### Inserted

 被绑定的元素插入父节点的时候调用(父节点存在即可调用，不必存在document中)

##### Update

 被绑定与元素所在模板更新时调用，而且无论绑定值是否有变化，通过比较更新前后的绑定值，忽略不必要的模板更新

##### componentUpdate

 被绑定的元素所在模板完成一次更新更新周期的时候调用

##### Unbind

 只调用一次，指令月元素解绑的时候调用