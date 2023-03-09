---
title:  Vue2的基本使用方法
date: 2021-06-18 16:33:32
tags: vue
swiper: true
swiperDesc: '
</br>vue
</br>vue-router
</br>vuex
'
top: true
categories: 经验总结
---

vue-style-loader 可以热更新

scoped的实现是借助了PostCSS实现的，一旦增加了scoped，他会将之前覆盖的样式转换成下面的样式

```
<style lang="scss">
.content[data-v-67e6b31f] {
  .title-wrap[data-v-67e6b31f] {
    font-size: 20px;
    color: red;
  }
}
</style>
```

如果你希望scoped中的某个样式能够作用的更深，影响到子组件，你可以使用`>>>`操作符

```
  cssModules: {
   localIdentName: isDev ? '[path]-[name]-[hash:base64:5]' : '[hash:base64:5]',
  }, // 在vue-loader-config中配置modules
```

##### 设置指定的Vue版本

```
    devServer,
    // import Vue from 'vue' 默认是vue.runtime.commonjs
    // 开发环境 vue.runtime.esm.js
    // 正式环境 vue.runtime.min.js
    // runtime 区别：简单来说，vue.js 是完整版的 Vue，拥有全部的组件（如 compiler）,而非完整版则没有。但也正因为没有 compiler，非完整版是不能使用 template ，需要使用 h 函数来渲染页面，开发体验特别糟糕。使用该版本，可直接将内容写在HTML中查看视图效果，或用template 渲染到 HTML，用webpack引入，需要配置alias，@vue/cli 引入也需要额外配置。
    resolve: {
        alias: {
            'vue': path.join(__dirname, '../node_modules/vue/dist/vue.esm.js')
        }
    },
```

```
  new HTMLPlugin({
    template: path.join(__dirname, './template.html') // 本地模板文件的位置
  })
```

## Vue 的实例

```
const app = new Vue({
  el: '#root', // app.$mount('#root') 等价
  template: '<div>this is content</div>'
}) // 挂载节点，整体替换掉
```

```
console.log(app.$data); // data里的数据
console.log(app.$props); // 传入的属性，外部属性
console.log(app.$el); // 挂载的html节点的引用
console.log(app.$options); // 传递的和Vue实例的初始化相关 使用 vm.$options 可以获取 实例上自定义的  属性
app.$options.data.text = 2  // 修改值失败 传入的options的data是通过vue在init时做过修改，并不是直接引入options.data 
子组件的$options.data().xx是数据初始化信息
app.text 和 app.$data.text 是同一个属性
app.$options.render = (h) => {
  return h('div', {}, 'new render function')
} // 页面不变化，有值下一次渲染时生效
app.$root === app
app.$children <item><div></div><item>  // div作为children传入到item 组件相关
app.$slots
app.$scopeSlots // template 中书写
app.$refs // 模板的引用 <div ref="div"></div> html节点返还html节点对象，是组件，返回组件实例
app.$isServer SSR用到 

```

```
app.$watch('text', (newText, oldText) => {
	this.$data.text + 5 // 报错
  console.log(`${newText} : ${oldText}`);
})
等价于
  watch: {
    text: function(newText, oldText){
      console.log(`${newText} : ${oldText}`);
    }
  }
  注意，不应该使用箭头函数来定义 watcher 函数 (例如 searchQuery: newValue => this.updateAutocomplete(newValue))。理由是箭头函数绑定了父级作用域的上下文，所以 this 将不会按照期望指向 Vue 实例，this.updateAutocomplete 将是 undefined。
  选项：deep：为了发现对象内部值的变化，可以在选项参数中指定 deep: true。注意监听数组的变更不需要这么做。
  
  通过$watch 需要自己注销 监听
  为什么要注销 watch？因为我们的组件是经常要被销毁的，比如我们跳一个路由，从一个页面跳到另外一个页面，那么原来的页面的 watch 其实就没用了，这时候我们应该注销掉原来页面的 watch 的，不然的话可能会导致内置溢出。
  const unWatch = app.$watch('text', function(newText, oldText) {
  this.$data.text + 5
  console.log(`${newText} : ${oldText}`);
})
unWatch() // app.$watch会返回一个注销方法 
```

```
自定义事件监听和触发
app.$on('test', () => console.log('change'))
app.$emit('test') // 不会冒泡 作用在同一个对象

app.$on('test', (a ,b) => console.log('change'+ ` ${a} ${b}`))
app.$emit('test', 1, 2) // 传值和接收

app.$once 只监听一次
app.$off 注销事件
```

```
app.$forceUpdate() // 迫使 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件 Vue是声明式渲染 在data中声明才触发响应式
app.$set(app.obj, 'a', i)
app.$delete()
```

```
app.$nextTick([callback]) // Vue 渲染是异步的  
methods: {
  updateMessage: async function () {
    this.message = '已更新'
    console.log(this.$el.textContent) // => '未更新'
    await this.$nextTick()
    console.log(this.$el.textContent) // => '已更新'
  }
} 

  methods: {
    updateMessage: function () {
      this.message = '已更新'
      console.log(this.$el.textContent) // => '未更新'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => '已更新'
      })
    }
  }
```

## Vue 生命周期

```
    beforeCreate () {
        console.log(this, 'beforeCreate');
        // 初始化时执行
    },
    created () {
        console.log(this, 'created');
        // 完成响应式
    },
    beforeMount () {
        console.log(this, 'beforeMount');
        // 不挂载到el上 不执行
    },
    mounted () {
        console.log(this, 'mounted');
        // 和DOM有关的放在这里
        // 数据有关放在 created 和 mounted 都可以
        // 服务端渲染不调用
    },
    activated () {
        console.log(this, 'activated');
        // keep-alive 相关
    },
    deactivated () {
        console.log(this, 'deactivated');
    },
    beforeUpdate () {
        console.log(this, 'beforeUpdate');
    },
    updated () {
        console.log(this, 'updated');
        // 数据更新执行
    },
    beforeDestroy () {
        console.log(this, 'beforeDestroy');
    },
    destroyed () {
        console.log(this, 'destroyed');
    },
```

```
    beforeMount () {
        console.log(this.$el, 'beforeMount');
        <div id="root"></div>
        // 不挂载到el上 不执行
    },
    mounted () {
        console.log(this.$el, 'mounted');
        // <div>1<div>
    },
```

```
    render (h) {
        return h('div', {}, this.text)
    },等价于
     template: '<div>{{text}}</div>'
     // 再beforeMount 和 mounted 之间执行
```

```
    renderError(h, err ) {
        return h("div", {}, err.stack)
    }, // 开发环境使用，只会作用于该组件，作用不到子组件 
```

```
	errorCaptured() {
        
    }, // 线上使用，可以作用到子组件，除非子组件禁止冒泡
```

## Data-binding

```
    var data = 'xxx' // template 无法访问
    
    template: `
    <div>
        {{isActive ? "W" : "Q"}}
    </div>
    ` // 可以做简单逻辑运算 arr.push() Date.now() 三元运算 自己定义在外层的变量不能访问
```

## Computed And Watch

```
const app = new Vue({
    el: "#root",
    data: {
        firstName: "Wang",
        lastName: "Qun",
        number: 1
    },
    template: `
    <div>
        {{getFullName()}}
        </br>
        {{fullName}}
        </br>
        {{number}}
    </div>
    `,
    computed: {
        fullName() {
            console.log("computed");
            return this.firstName + ' ' + this.lastName
        }
    },
    methods: {
        getFullName() {
            console.log("methods");
            return this.firstName + ' ' + this.lastName
        }
    }
})

setInterval(() => {
    app.number++
}, 5000) // 页面数据改变，重新渲染，执行getFullName(),而computed依赖的数据不变，不重新计算
```

```
        fullName: {
            get() {
                console.log("computed");
                return this.firstName + ' ' + this.lastName
            },
            set (fullName) {
                const names = fullName.split(' ');
                this.firstName = names[0]
                this.lastName = names[1]
            }
 // cpmputed 可以设置值，但不推荐这样做，组合容易，拆分难，容易出错，重复计算
        
```

```
   {{fullName_watch}}
   
   watch: {
        firstName: {
            firstName (newVal, oldVal) {
                this.fullName_watch = newVal + '' + this.lastName
            } // watch最初绑定的时候不会执行。可以通过添加handler方法
            // 声明时不执行，有变化时执行
        },
    },
    
        watch: { // 不设置deep时浅监听，只监听一层 性能好
        firstName: {
            handler(newVal, oldVal) {
                this.fullName_watch = newVal + '' + this.lastName
            },
            immediate: true // 立即先执行一次handler
            deep: true // 该回调会在任何被侦听的对象的 property 改变时被调用，不论其被嵌套多深
        }
    }, // 监听变化，给后台发请求computed做不到，只能watch去做 监听到某一个数据变化，去做指定的操作
    如想提升性能，可以用字符串包裹想要监听的属性
            'obj.a': {
            handler () {
                console.log("obj.a changed");
            },
            // deep: true

        	}
```

methods 和 computed 的区别

定义在methods中的函数：1.函数内依赖了data中属性，2.这些属性发生了改变。

methods函数主动调用时，像普通函数一样执行

定义在computed的函数：1.函数内依赖了vue的属性，2.这些属性发生了改变，3.这些属性被页面引用。

computed的函数主动调用时，其是否会执行和内部的依赖属性是否发生改变有关。

**不建议在computed和watch中修改任何值，可能会导致无限循环触发  ** 

## Vue的原生指令

```
v-text
<div v-text="text"></text> // 等价 
<div v-text=" 'Text:' + text"></text> // 标签内放入其他内容需要写成
<div>{{text}}</text> // 等价

v-html 可以写html代码片段
<div v-html="html"></div> // html: '<h2>text</h3>'

v-show 通过控制display:none的切换显示隐藏

v-if 通过渲染生成新的DOM节点显示隐藏 搭配 v-else-if v-else

v-on = @ v-bind = :

v-model 
用于输入组件 <input v-model="text"></input>
可以给自定义组件使用v-model

<input type="checkbox" v-model="active"></input> // active: false
  <div>
  <input type="checkbox" :value="1" v-model="arr"></input> // 如使用value="1" ‘1’是字符串 :value="1" 是数字
  <input type="checkbox" :value="1" v-model="arr"></input>
  <input type="checkbox" :value="1" v-model="arr"></input>
  </div>
  
 <input type="radio" value="one" v-model="picked"></input>
 <input type="radio" value="two" v-model="picked"></input> // 单选框
 
 修饰符
 .number 转换成数字
 .trim 删除首位空格
 .lazy 失去焦点修改数据
 
 v-pre 不编译内容
 v-cloak 在vue代码没加载前，v-cloak相当于加一个样式 display:none 代码加载完显示
 v-once 只修改一次 适用于静态内容 不做虚拟 DOM 对比，节约性能
```

## Component

定义组件

```
全局注册
const component = {
  template: `
  <div>this is component</div>
  `
}
Vue.component('CompOne', component) // 最好使用大写开头 Vue的组件可以看作是一个类 
new Vue ({
  el: "#root",
  template: `<comp-one></comp-one>` // 使用连接符
})

局部注册
new Vue ({
  el: "#root",
  template: `<comp></comp>`,
  components: {
    CompOne: component
  }
})
```

```
使用data定义组件内数据时，如果不是通过new Vue注册的，而是通过全局注册到某个Vue实例中使用时，要使用data function
  data () {
    return {
      text: '123'
    }
  }, // 保证每一个组件的data都是一个新对象，防止变量污染其他组件 不能使用全局对象

写法：
const data = {text: '123'}
const component = {
  data () {
    return data
  },
  template: `
  <input v-model="text"></input>
  `
}
new Vue ({
  el: "#root",
  template: `
  <div>
    <comp-one/>
    <comp-one/>
  </div>
  `,
  components: {
    CompOne: component
  }
}) // 输出时，保持对一个 data 的引用，改变一个，另外一个也随着改变
```

```
props
定义组件被外部组件使用时，可变的行为。组织组件可配置的行为。从父组件接收属性。
```

**数据不使用：绑定，以字符串的形式输出**  **绑定会解析传入的字符串**

单项数据流 子组件不能修改父组件传来的数据

```
修改传来属性的方法
<span @click="handleChange">{{propsOne}}</span>
  methods: {
    handleChange() {
      this.$emit('change')
    }
  }, // 子组件$emit 通过Vue实例的$emit comp-one 是一个Vue的实例
  
<comp-one :active="true" :props-one="propsOne" @change="handleChange"/>
  methods: {
    handleChange() {
      this.propsOne += 1
    }
  } // 父组件$on  @change = this.$on('change')
  
  <comp-one :active="true" 
  			:props-one="propsOne"
            ref="comp1" 
            @change="handleChange"/>
  // comp-one 是一个Vue的实例 有refs属性 效果与new Vue一样
```

```
props的数据验证
    active: {
      type: Boolean,
      required: true,
      default: false // 如果props接收一个对象 需要声明一个方法，在里面return 道理同data()
       validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }    // 自定义验证函数
    },
    
```

组件继承

```
const CompVue = Vue.extend(component)

new CompVue({
  name: "root", // name 挂载到 $options 而不是挂载在生成的实例上
  el: "#root",
  propsData: {
    propsOne: "xxx"
  } // 通过propsData 传入数据
  data: {
    text: '1220'
  } // 可以覆盖默认值
}) // 声明了一个组件

const component2 = {
  extends: component, // 继承
  data () {
    return {
      text: '180'
    }
  }
}
```

```
  mounted () {
    this.$parent.text = '123' // 不建议这样修改！
  }
  子组件可以通过$parent直接调用父组件属性
  const component2 = {
  parent: parent, // 不生效 vue渲染时指定 没办法修改
  extends: component,
  }
  new Vue({
  	parent: parent // new Vue(实例)的时候才能指定parent 
  }) 
```

### 组件高级特性

#### 插槽

```
  template: `
    <div>
      <comp-one>
        <span>this is content</span>
      </comp-one>
      {{value}}
    </div> // 实现span的显示
    `

  template: `
    <div :style="style">
      <slot></slot> // 可以在里面放默认内容，它只会在没有提供内容的时候被渲染
    </div>
  `, // 可以实现span 将span放在组件模板里面
  

  具名插槽
  <div class="container">
  <header>
    <!-- 我们希望把页头放这里 -->
  </header>
  <main>
    <!-- 我们希望把主要内容放这里 -->
  </main>
  <footer>
    <!-- 我们希望把页脚放这里 -->
  </footer>
</div>

<div class="container">
  <header>
    <slot name="header"></slot> // 不能有默认
  </header>
  <main>
    <slot>默认</slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
一个不带 name 的 <slot> 出口会带有隐含的名字“default”。
// 默认写法可以不带<template>
```

父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

```
在向具名插槽提供内容的时候，我们可以在一个 <template> 元素上使用 v-slot 指令，并以 v-slot 的参数的形式提供其名称：
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
注意 v-slot 只能添加在 <template> 上 (只有一种例外情况)，这一点和已经废弃的 slot attribute 不同。 v-slot ==== #
```

#### 作用域插槽

```
      <div>
        <slot name="c" :slotData="title"></slot>
      </div>
      
        <template #c="slotProps">
          {{slotProps.slotData.a}}
        </template> // 父组件访问子组件内容
        
        可以使用解构赋值
       <div>
        <slot name="c" :slotData="{value, title}"></slot>
      </div>
```

ref 用在组件返回该组件，用在html返回标签

```
provide 和 inject 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。

  provide () {
    return {
      grandFather: this,
      value: this.value
    }
  },
  
    inject: ['grandFather', 'value'],
    mounted() {
    	console.log(this.grandFather, this.value);
 	 },
```

```
处理父子、爷孙组件通讯。需要有上下关系。不具有响应式
如需要具有响应式：
  provide () {
    const data = {}
    Object.defineProperty(data, 'value' {
	  get: () => this.value, // 需要箭头函数
      enumerable:true // 可被读取
    })
    return data // 不建议使用
```

### render

使用template 会使用生命周期的一个过程——编译 编译成JS函数 通过render()

```
  render (createElement) {
    return createElement()
  } // 等价于
  render () {
  // 每一个实例都有$createElement方法
  	return this.$createElement()
  }
  
    render (createElement) {
    return createElement('comp-one', {attrs}, [children])
  }
```

## Vue-router

```
routes.js
import Todo from '../views/todo/todo.vue'
import Login from '../views/login/login.vue'
export default [
  {
    path: '/app',
    component: Todo
  },
  {
    path: '/login',
    component: Login
  }
] // 最基本的配置
```

```
touter.js
import Router from 'vue-router'

import routes from './routes'

const router = new Router({
  routes
}) 

export default router // import 的router 都是同一个router
```

```
import Router from 'vue-router'
import routes from './routes'
// export default router // import 的router 都是同一个router
export default () => {
  return new Router({
    routes
  })
} // 项目需要用到服务端渲染，如果只引用一个 router 会有内存溢出问题 
每次服务端渲染都会重新生成一个App
```

```
index.js配置路由
import Vue from 'vue'
import App from './app.vue'
import VueRouter from 'vue-router'

import './assets/styles/global.styl'
import createRouter from './config/router'

Vue.use(VueRouter)
const router = createRouter()

const root = document.createElement('div')
document.body.appendChild(root)

new Vue({
  router,
  render: (h) => h(App)
}).$mount(root)
```

```
app.vue
<template>
  <div id="app">
    <div id="cover"></div>
    <Header></Header>
    <!-- <todo></todo> -->
    <router-view /> // 添加路由跳转
    <Footer></Footer>
  </div>
</template>

  {
    path: '/',
    redirect: '/app'
  }, // 默认页面
```

http://localhost:8000/#/app hash路由不会被SEO 

```
 // router.js
export default () => {
  return new Router({
    routes,
    mode: 'history', // 改成H5路由，没有#
    base: '/base/' // http://localhost:8000/base/app  基路径
    linkActiveClass: '',
    linkExactActiveClass: ''，
    scrollBehavior (to, from, savePosition) {
      if (savePosition) return savePosition
      return {x: 0, y: 0}
    } // 记录上次访问页面滚动的位置
    parseQuery (query) {
	// 记录参数 http://localhost:8000/app?=undefined
    },
    stringifyQuery (obj) {
    },
    fallback: true // 检测到浏览器不支持H5， 转成hash路由

  })
}
```

```
路由跳转
    <router-link to="/app">app</router-link>
    <router-link to="/login">login</router-link>
    <router-view /> // 页面显示
```

```
const devServer = {
  port: 8080,
  host: '0.0.0.0',
  overlay: {
    errors: true, // 出现错误，编译器全屏覆盖
  },
 	historyApiFallback: {
    index: '/index.html'
  },  // webpack devServer没有做路径的映射关系，http://localhost:8000/app不认识/app路径，页面刷新时会与后端交互。在前端跳转没有问题   history路由 可以配置 historyApiFallback
  hot: true
}
```

路由的命名

可以通过 name  进行路由跳转

```
<router-link :to="{name: 'app'}">app</router-link>
```

```
meta: {
	// 保存路由信息 SEO
	title: 'this is app',
	discription: 'asdasd'
}
```

```
    children: [
      {
        path: 'text',
        component: Login
      }
    ] // 需要在该组件声明<router-view /> 占位符
```

```
    <transition name="fade" mode="out-in">
      <router-view />
    <transition> // 过渡动画
```

```
path: '/app/:id', // 路由传参
<router-link to="/app/123">app</router-link>
  mounted() {
    console.log(this.$route);
  },
  query: ?a=123&b=46,
  
  path: '/app/:id', 
  props: true, // 会把:id 作为prop传入组件
  // props 可以指定传入的数据
  // props: {id: 456}  props: (router) =>( {id: router.query.b} )
  
  todo.vue
  props: ['id'],
```

#### 路由守卫

```
index.js
Vue.use(VueRouter)
const router = createRouter()

router.beforeEach((to, from, next) => {
  console.log('beforeEach invoked'); 
  if (to.fullPath === '/login')  // 进行数据校验 如页面需要用户登录 跳转登录页面
  next("/login")
})
router.beforeResolve((to, from, next) => {
  console.log('beforeResolve invoked');
  next()
})
router.afterEach((to, from) => {
  console.log('afterEach invoked');
})

routes.js
meta: {
      title: 'this is app',
	    discription: 'asdasd'
    },
    beforeEnter: (to, from, next) => {
      // 进入路由之前被调用
    }

todo.vue
  beforeRouteEnter (to, from, next) {
  	// 不能调用this 页面没有渲染
    console.log('beforeRouterEnter invoked');
    next() //     next(vm => {console.log(vm.$options.name)}) 在next中调用
  },
  beforeRouterUpdate (to, from, next) {
    console.log('beforeRouterUpdate invoked');
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    不触发组件 mounted 生命周期
    next()
  },
  beforeRouteLeave (to, from, next) {
    console.log('beforeRouteLeave invoked');
    if ( confirm("are u sure?") ) next() // 用户确认是否要离开
  },
    顺序
beforeEach invoked
beforeEnter invoked
beforeRouterEnter invoked
beforeResolve invoked
afterEach invoked

```

#### 命名视图

```
<router-view name="a" />
 // routes.js
    components: {
      default: Todo, // 没名字的
      a: Login
    },
```

#### 异步组件

```
component: () => import('../views/todo/todo.vue') // 需要bebal-plugin-syntax-dynamic-import

修改.babelrc
  "plugins": [
    "syntax-dynamic-import"
  ] // 首屏加载速度变快
```

## Vuex

Flux 单向数据流

```
初始化store
import Vuex from 'vuex'
import Vue from 'vue'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0
  }，
    mutations: {
    updateCount (state, num) {
      state.count = num
    }
  }
}
export default store // 一个Vuex实例

new Vue({
  router,
  store, // vue 是树形结构，放在最外面，所有子节点才能拿到
  render: (h) => h(App)
}).$mount(root)
```

```
app.vue
  mounted() {
    console.log(this.$store);
  }, // 调用 store 在每一个组件都加入了$store
  computed: {
    count () {
      return this.$store.state.count
    }
  }
```

```
修改值
  mounted() {
    console.log(this.$store);
    let i = 1;
    setInterval(() => {
      this.$store.commit('updateCount', i++)
    }, 1000)
  },
```

最好把state单独拿出来，未声明的变量，设置一个null(触发响应式)

```
getters.js
export default {
  // 组件内的 computed 方便生成在应用中可以用的数据
  fullName (state) {
    return state.fullName + ' ' + state.fullName
  }
}

app.vue
  computed: {
    fullName () {
      return this.$store.getters.fullName
    }
  }
```

```
更方便的使用vuex
app.vue
import {mapState, mapGetters} from 'vuex'

  computed: {
    ...mapState(['count']), // 等价于
    // count () {
    //   return this.$store.state.count
    // },
    
    ...mapState({
      counter: (state) => state.count
    }),// 重命名
```

```
mutations // 同步
updateCount (state, num) // 只能转2个参数，第二个参数是包含修改state的对象 
updateCount (state, {num: i++, num2: 2})

所有的state数据修改都要放在mutations中
```

```
store.js
export default () => {
  return new Vuex.Store({
    strict: true, // 无法在外部通过this.$store.state.xxx 修改数据 在开发环境规范代码使用
  })
}

const isDev = process.env.NODE_ENV === 'development'
strict: isDev,
```

```
actions // 异步

  updateCountAsync (store, data) {
    setTimeout(() => {
      store.commit("updateCount", data.num)
    }, data.time)
  }
  
app.vue
  mounted() {
    this.$store.dispatch("updateCountAsync", {
      num: 10,
      time: 2000,
    })
  },
```

```
更方便的使用 mapMutations, mapActions
import {mapMutations, mapActions} from 'vuex'
  methods: {
    ...mapActions(['updateCountAsync']),
    ...mapMutations(['updateCount'])
  },
    mounted() {
    this.updateCountAsync({
      num: 10,
      time: 2000,
    })
  },
  对比：
  mounted() {
    this.$store.dispatch("updateCountAsync", {
      num: 10,
      time: 2000,
    })
  },
```

#### Vuex 模块

```
store.js
export default () => {
  return new Vuex.Store({
    modules: {
      a: {
      	// namespaced: true
        state: {
          text: 1
        },
         mutations: {
          updateText (state, text) {
            // 这里的state是 a 模块的state 默认把所有的mutations 放在全局命名空间 如需要单独命名空间 需要声明一个属性，namespaced: true
            state.text = text
          }
        }
      },
      b: {
        state: {
          text: 2
        }
      }
    }
  })
  
  app.vue
  computed: {
  	textA () {
  		return this.$store.state.a.text
  	}
  }
  
  使用 mapState 不能通过名字来调用
  ...mapState({
  	textA: state => state.a.text
  }) // 通过对象映射关系
  
  使用 mapMutations 可以通过名字调用
  使用 namespaced 时：
  ...mapMutations(['updateCount', 'a/updateText'])
  this['a/updateText']('321')
 
```

```
        getters: {
          textPlus (state, getters, rootState) {
            return state.text +rootState.count // 第三个参数：全局state
          }
        }
        
         actions: {
          add ( {state, commit, rootState} ) {
            commit('updateText', rootState.count)
          }
        } 
        // 需要从全局mutations调用:commit('updateText', rootState.count {root:true})
```

```
index.js
store.registerModule('c', {
  state: {
    text: 3
  }
}) // 动态加载模块

store.unregisterModule('c') // 解绑
```

```
修改store 需要刷新页面更新 
使用热更替

export default () => {
  const store =  new Vuex.Store({
    strict: isDev,
    state: defaultState,
    mutations,
    getters,
    actions,
    
  if (module.hot) {
    module.hot.accept([
      './state/state.js',
      './mutations/mutations',
      './getters/getters',
      './actions/actions'
    ], () => {
      const newState = require('./state/state').default
      const newMutations = require('./mutations/mutations').default
      const newGetters = require('./getters/getters').default
      const newActions = require('./actions/actions').default

      store.hotUpdate({
        state: newState,
        mutations: newMutations,
        getters: newGetters,
        actions: newActions
      })
    })

    return store
  }
}
```

```
index.js
监听 制作插件使用 vuex中的插件就一个function
store.watch((state) => state.count + 1, (newCount) => {
  console.log(newCount)
})

store.subscribe((mutations, state) => {
  console.log(mutations.type, state);
    console.log(mutations.payload, state); // 调用的方法
})

store.subscribeAction((action, state) => {
  console.log(action);
})  // 规定mutations action 第二个参数必须是obj,在subcribe时能把所有参数拿到，一目了然的去操作一些东西

    plugins: [
      (store) => {
        // 初始化调用
        console.log(store.subscribe);
      }
    ],
```
