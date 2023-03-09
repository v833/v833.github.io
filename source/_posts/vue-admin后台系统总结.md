---
title: vue-admin 后台系统总结
date: 2021-07-09 18:22:32
tags: vue
categories: 项目总结
---

```
vue create vue-admin
```

## 项目初始化

npm i es-lint

导入vue.config.js

创建styles文件夹并导入normalize.css

## 登录页面

### 使用elementui

```
npm i element-ui -S 
```

```
main.js
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
Vue.use(ElementUI);
```

### 表单验证

```
<el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
  <el-form-item label="密码" prop="pass">
    <el-input type="password" v-model="ruleForm.pass" autocomplete="off"></el-input>
  </el-form-item>
  <el-form-item label="确认密码" prop="checkPass">
    <el-input type="password" v-model="ruleForm.checkPass" autocomplete="off"></el-input>
  </el-form-item>
  <el-form-item label="年龄" prop="age">
    <el-input v-model.number="ruleForm.age"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
    <el-button @click="resetForm('ruleForm')">重置</el-button>
  </el-form-item>
</el-form>
<script>
  export default {
    data() {
      var checkAge = (rule, value, callback) => {
        if (!value) {
          return callback(new Error('年龄不能为空'));
        }
        setTimeout(() => {
          if (!Number.isInteger(value)) {
            callback(new Error('请输入数字值'));
          } else {
            if (value < 18) {
              callback(new Error('必须年满18岁'));
            } else {
              callback();
            }
          }
        }, 1000);
      };
      var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          if (this.ruleForm.checkPass !== '') {
            this.$refs.ruleForm.validateField('checkPass');
          }
          callback();
        }
      };
      var validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value !== this.ruleForm.pass) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };
      return {
        ruleForm: {
          pass: '',
          checkPass: '',
          age: ''
        },
        rules: {
          pass: [
            { validator: validatePass, trigger: 'blur' }
          ],
          checkPass: [
            { validator: validatePass2, trigger: 'blur' }
          ],
          age: [
            { validator: checkAge, trigger: 'blur' }
          ]
        }
      };
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
  }
</script>
```

```
邮箱验证规则
let reg = /^([a-zA-Z0-9]+[_|_|\-|.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[_|_|.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,3}$/
```

```
密码验证
标签上添加minlength="6" maxlength="20" 输入限制 最小6位，最大29位
只能输入6-20个字母+数字
let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/
value = this.ruleForm.password = stripscript(value)
```

```
./utils/validate.js

过滤特殊字符
export function stripscript(s) {
    var pattern = new RegExp("[`~!@#$^&*()=|{}':;',\\[\\].<>/?~！@#￥……&*（）&—|{}【】‘；：”“'。，、？]")
    var rs = "";
    for (var i = 0; i < s.length; i++) {
            rs = rs + s.substr(i, 1).replace(pattern, '');
        }
    return rs;
    }

// 验证用户名
export function validateEmail(value) {
  let reg = /^([a-zA-Z0-9]+[_|_|\-|.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[_|_|.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,3}$/
  return reg.test(value)
}

// 验证密码
export function validatePass(value) {
  let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/
  return reg.test(value)
}
```

### vue2.0 =>vue3.0

```
安装依赖
npm install @vue/composition-api --save

Main.js：
import VueCompositionApi from '@vue/composition-api'; Vue.use(VueCompositionApi);
```

vue3.0对vue的主要3个特点：**响应式、模板、对象式的组件声明方式**，进行了全面的更改，底层的实现和上层的api都有了明显的变化，基于Proxy重新实现了响应式，基于treeshaking内置了更多功能，提供了类式的组件声明方式。

```
2.x生命周期选项和Composition API之间的映射
beforeCreate ->使用 setup()
created ->使用 setup()
beforeMount - > onBeforeMount
mounted - > onMounted
methods -> 去除，普通方式写方法
beforeUpdate - > onBeforeUpdate
updated - > onUpdated
beforeDestroy - > onBeforeUnmount
destroyed - > onUnmounted
errorCaptured - > onErrorCaptured

```

**Reactive（声明单一对象时使用）**

取得一个对象并返回原始对象的响应数据处理。

const obj = reactive({ count: 0 })

**ref（声明基础数据类型变量时使用）**

内部值并返回一个响应性且可变的ref对象。ref对象具有.value指向内部值的单个属性。

const number = ref(0);

获取值方式：number.value

**isRef** **和** **toRefs**

检查一个对象是否是ref对象：

```
const unwrapped = isRef(foo) ? foo.value : foo;
function useMousePosition() {
  const pos = reactive({
   x: 0,
   y: 0
  });
  return toRefs(pos);
}
const { x, y } = useMousePosition();
```

toRefs将reactive对象转换为普通对象，保证对象解构或拓展运算符不会丢失原有响应式对象的响应。

**watch** **侦听器**

```
const count = ref(100);
watch(()=>count.vlaue,()=>{
  console.log('count数值发生变化了')
})
count.value = 200; // count重新赋值，watch则被执行
```

**Computed**

可传入get和set，用于定义可更改的计算属性

```
const count = ref(1);
const plusOne = computed({
    get: () => count.value + 1,
    set: val => { count.value = val - 1 }
});
plusOne.value = 1;
console.log(count.value); // 0
```



```
  import { reactive } from '@vue/composition-api'
  setup(props, context) {
    // 这里放置data数据，生命周期，自定义的函数
  },
  setup(props, { refs }) {
	// 解构写法
	context.attrs
	context.slots
	context.parent
	context.root // 根组件
	context.refs
	function aaa() {
	  reuturn {
		attrs: 0,
		slots: 1,
		parent: 2,
		root: 3
	  }
	}
	let { attrs: qq, slots, parent, root } = aaa()
  }
```

## axios

```
npm install axios
```

```
      axios.request({
        method: 'post',
        timeout: 3000,
        url: '/user',
        data: {
          firstName: 'x',
          lastName: 'y'
        }
```

### axios拦截器

```
utils request.js
import axios from 'axios'

// 创建axios, 赋给变量
const service = axios.create()
// 添加请求拦截器
service.interceptors.request.use(function (config) {
  // 在发送请求之前做些什么
  // 请求头 后台需要前端传相关的参数
  return config
}, function (error) {
  // 对请求错误做些什么
  return Promise.reject(error)
})

// 添加响应拦截器
service.interceptors.response.use(function (config) {
  // 在对响应数据做些什么
  return config
}, function (error) {
  // 对响应错误做些什么
  return Promise.reject(error)
})

export default service
```

### vue 的环境变量

你可以在你的项目根目录中放置下列文件来指定环境变量：

```bash
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```

**模式**是 Vue CLI 项目中一个重要的概念。默认情况下，一个 Vue CLI 项目有三个模式：

- `development` 模式用于 `vue-cli-service serve`
- `test` 模式用于 `vue-cli-service test:unit`
- `production` 模式用于 `vue-cli-service build` 和 `vue-cli-service test:e2e`

你可以通过传递 `--mode` 选项参数为命令行覆写默认的模式。例如，如果你想要在构建命令中使用开发环境变量：

```text
vue-cli-service build --mode development
```

当运行 `vue-cli-service` 命令时，所有的环境变量都从对应的[环境文件](https://cli.vuejs.org/zh/guide/mode-and-env.html#环境变量)中载入。如果文件内部不包含 `NODE_ENV` 变量，它的值将取决于模式，例如，在 `production` 模式下被设置为 `"production"`，在 `test` 模式下被设置为 `"test"`，默认则是 `"development"`。

`NODE_ENV` 将决定您的应用运行的模式，是开发，生产还是测试，因此也决定了创建哪种 webpack 配置。

例如通过将 `NODE_ENV` 设置为 `"test"`，Vue CLI 会创建一个优化过后的，并且旨在用于单元测试的 webpack 配置，它并不会处理图片以及一些对单元测试非必需的其他资源。

同理，`NODE_ENV=development` 创建一个 webpack 配置，该配置启用热更新，不会对资源进行 hash 也不会打出 vendor bundles，目的是为了在开发的时候能够快速重新构建。

当你运行 `vue-cli-service build` 命令时，无论你要部署到哪个环境，应该始终把 `NODE_ENV` 设置为 `"production"` 来获取可用于部署的应用程序。

NODE_ENV

如果在环境中有默认的 `NODE_ENV`，你应该移除它或在运行 `vue-cli-service` 命令的时候明确地设置 `NODE_ENV`。

```
.env.development
VUE_APP_ABC = 111 // 格式为VUE_APP_title

console.log(process.env.VUE_APP_ABC) // 111
```

**请注意，只有 `NODE_ENV`，`BASE_URL` 和以 `VUE_APP_` 开头的变量将通过 `webpack.DefinePlugin` 静态地嵌入到*客户端侧*的代码中。这是为了避免意外公开机器上可能具有相同名称的私钥。**

### 跨域 设置代理proxy

```
    vue.config.js
    proxy: {
      [process.env.VUE_APP_API]: {
        target: 'http://www.web-jshtml.cn/productapi',
        changeOrigin: true,
        pathRewrite: {
          ['^' + process.env.VUE_APP_API]: ''
        }
      }
    }, // 设置代理
```

## 测试用例
![5、测试用例](https://img11.360buyimg.com/ddimg/jfs/t1/192669/13/11737/857084/60e91529E7bc30eb7/2f6729ec49479923.jpg)

![6、程序流程图](https://img11.360buyimg.com/ddimg/jfs/t1/189951/15/12681/109277/60e91531E9aca2602/38ec3bdc82d040d5.jpg)

```
// 添加响应拦截器
import { Message } from 'element-ui'

service.interceptors.response.use(function (response) {
  // 在对响应数据做些什么
  let data = response.data
  if (data.resCode !== 0) {
    Message.error(data.message) // 在js文件需要单独引入
    return Promise.reject(data)
  }
  return data // 等价于 Promise.resolve(data)
}, function (error) {
  // 对响应错误做些什么
  return Promise.reject(error)
})
```

```
    // 获取验证码
    const handleGetSms = (async () => {
      if (ruleForm.username === '') {
        root.$message.error('用户名不能为空')
        return
      }
      if (!validateEmail(ruleForm.username)) {
        root.$message.error('用户名格式错误，请重新输入')
        return
      }
      loginButtonState.value = false
      let data = await getSms({ username: ruleForm.username })
      root.$message.success(data.message)
    })
```

```
// login.js
import service from '@/utils/request.js'
// 获取验证码接口
export function getSms (data) {
  return service.request({
    method: 'post',
    url: '/getSms/',
    data
  })
}
```

```
自动聚焦
<lable for="xx"></lable>
<input id="xx" />
```

```
 // 重置表单
 refs['ruleForm'].resetFields()
```

```
设计 计时setInterval 超时timeout 功能
    const handleGetSms = (() => {
      if (ruleForm.username === '') {
        root.$message.error('用户名不能为空')
        return
      }
      if (!validateEmail(ruleForm.username)) {
        root.$message.error('用户名格式错误，请重新输入')
        return
      }
      codeButtonState.status = true
      codeButtonState.text = '发送中'
      let requestData = {
        username: ruleForm.username,
        module: menuTab[0].current ? 'login' : 'register'
      }
      setTimeout(async () => {
        let data = await getSms(requestData)
        root.$message.success(data.message)
        loginButtonState.value = false
        // 调用定时器，倒计时60s
        countDown(60)
      }, 2000);
    })
    // 倒计时
    const countDown = ((value) => {
      let time = value
      // 判断是否有定时器，如果有则清除
      if (timer.value) clearInterval(tiner.value)
      timer.value = setInterval (() => {
        codeButtonState.text = --time
        if (time === 0) {
          clearInterval(timer.value)
          codeButtonState.text = '再次获取'
          loginButtonState.value = true
          codeButtonState.status = false
        }
      }, 1000)
    })
```

```
// 注册请求
export function postRegister (data) {
  return service.request({
    method: 'post',
    url: '/register/',
    data
  })
}
```

```
    // 注册表单提交
    const submitForm = ((formName) => {
      refs[formName].validate((valid) => {
        if (valid) {
          let data = {
            username: ruleForm.username,
            password: ruleForm.password,
            code: ruleForm.code
          }
          postRegister(data)
          .then((responce) => {
            let data = responce.data
            root.$message.success(data.message)
            // 模拟注册成功
            toggleMenu(menuTab[0])
            clearCountDown()
          })
        } else {
          root.$message.error('error submit')
          return false
        }
      })
    })
```

```
// 清除倒计时
    const clearCountDown = (() => {
      codeButtonState.state = false,
      codeButtonState.text = '获取验证码'
      clearInterval(timer.value)
    })
```

## 密码加密 

### js-sha1

`sha1`和`md5`加密是不可逆的 加密后无法解密

```
npm i js-sha1
```

```
import sha1 from 'js-sha1'

password: sha1(ruleForm.password),
```

### 加密流程

在前端先加密一次

后台接收到字符串再次加密

最后新的字符串写入数据库

## 后台页面搭建

```
router.js
  {
    path: '/console',
    name: 'console',
    component: () => import('../views/Console')
  }
```

### vue router

```
router-link
// 不带参数
<router-link :to: "{ name: 'xx' }">
<router-link :to: "{ path: '/xx' }">
<a href="xx" />

// 带参数
<router-link :to: "{ name: 'xx', params: {id: 1} }">
// 路由配置 path: "/xx/:id" 或者 path: '/xx:id'
// url的参数不可见，刷新后参数会消失

<router-link :to: "{ path: '/xx', query: { id: 1 }}">
// 路由可不配置
// url带的参数是可见的，刷新后参数不会消失
// query 传递的参数不能是对象
```

```
this.$router.push() 函数里面调用
// 不带参数
this.$router.push( {name: 'xx'} )
this.$router.push( {path: '/xx'} )

// query 传参
this.$router.push({
	path: '/xx',
	query: { id: 1 }
}) 
// url 带的参数是可见的，刷新后参数不会消失

// params 传参
this.$router.push({
	name: 'xx',
	params: { id: 1 }
}) 
// 路由配置 path: "/xx/:id" 或者 path: '/xx:id'
// url的参数不可见，刷新后参数会消失
// 不配置path,刷新页面参数消失
// 配置path，刷新页面id保留

// query 和 params 区别
query 跳转配合路由path属性，传参为明文，url上参数可见，刷新后参数不会消失
params 跳转配合路由name属性，传参为密文，url上参数不可见，刷新后参数会消失
```

### 导航菜单设置

使用elementui导航菜单

```
    <el-menu
      default-active="1-4-1"
      class="el-menu-vertical-demo"
      @open="handleOpen"
      @close="handleClose"
      :collapse="isCollapse"
      background-color="transparent"
      text-color="#fff"
      active-text-color="#fff"
      router // 启用时以:index的参数为path进行跳转
    >
```

```
//使用route属性meta携带参数传参
  {
    path: '/',
    redirect: '/console',
    hidden: true,
    meta: {
      name:
    }
  },
 
//接收参数
const routes = this.$router.options.routes
```

```
// vue中如果想同时使用v-for 和 v-if， 可以使用computed 或者 < template>标签 vue中template没有实际意义
      <template v-for="(item, index) in routes">
        <el-submenu
          :index="index"
          v-if="!item.hidden"
          :key="item.id"
        >
```

```
// 修改element全局样式需要单独修改
// elementui.scss
.el-submenu .el-menu-item.is-active {
  background-color: #f56c6c !important;
}
```

### svg-icon制作

```
// 在路由中配置meta.icon
<i :class="item.meta.icon"></i>
```

```
// 在vue中全局定义组件 需要引入vue的complile版本
```

```
    <svg :class="svgClass" aria-hidden="true">
      <use :xlink:href="name"></use>
    </svg>
```

```
// 解析svg
const req = require.context('./svg', false, /\.svg$/)
const requireAll = requireContext => {
  return requireContext.keys().map(requireContext)
}
requireAll(req)

// require.context(‘./svg’, false, /\.svg$/) 参数说明：
// 第一个：目录
// 第二个：是否遍历子级目录
// 第三个：定义遍历文件规则
```

```
// vue.config.js
  chainWebpack: (config) => {
    const svgRule = config.module.rule("svg");
    svgRule.uses.clear();
    svgRule
      .use("svg-sprite-loader")
      .loader("svg-sprite-loader")
      .options({
        symbolId: "icon-[name]",
        include: ["./src/icons"]
      });
  },
```

```
npm install svg-sprite-loader -S
```

### vuex

state： 是存储的单一状态，是存储的基本数据

getters：getters是store的计算属性，对state的加工，是派生出来的数据。

mutations：对数据进行计算的方法全部写在里面（类似computed） 在页面中触发时使用

this.$store.commit('mutationName')触发Mutations方法改变state的值

actions：action的功能和mutation是类似的，都是去变更store里的state，不过action和mutation有两点不同：

1、action主要处理的是异步的操作，mutation必须同步执行，而action就不受这样的限制，也就是说action中我们既可以处理同步（视图触发Action，Action再触发Mutation），也可以处理异步的操作

2、action改变状态，最后是通过提交mutation

this.$store.dispatch(actionName)

3、角色定位基于流程顺序，二者扮演不同的角色。

Mutation：专注于修改State，理论上是修改State的唯一途径。

Action：业务代码、异步请求。

### 菜单导航收起

```
// 兼容性处理
@mixin webkit($type, $value){
  -webkit-#{$type}: $value;
  -moz-#{$type}: $value;
  -o-#{$type}: $value;
  -ms-#{$type}: $value;
  #{$type}: $value;
}

@include webkit(transition, all .3s ease 0s);
// transition: property duration timing-function delay;
```

### 存储方法

```
// 页面刷新时数据初始化，需要缓存数据
```

```
cookie.js
npm i cookie_js -save

import Cookie from 'cookie_js'
Cookie.set('isCollapse', JSON.stringify(state.isCollapse))
```

```
window.sessionStorage.setItem('isCollapse', JSON.stringify(state.isCollapse))
isCollapse: JSON.parse(sessionStorage.getItem('isCollapes')) || false
```

```
  actions: {
    login(content, data) {
      return new Promise((resolve, reject) => {
        // 接口
        postLogin(data).then((response) =>{
          resolve(response)
        }).catch(error => {
          reject(error)
        })
      })
    }
  },
```

### token

![image-20210630112304654](https://img11.360buyimg.com/ddimg/jfs/t1/176686/19/17349/270236/60dbe3deEe5dce88e/97237fc41076396b.png)

```
// 全局前置守卫
// router.beforeEach 路由跳前之前 
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是**异步**解析执行，此时导航在所有守卫 resolve 完之前一直处于 **等待中**。

每个守卫方法接收三个参数：

- **`to: Route`**: 即将要进入的目标路由对象
- **`from: Route`**: 当前导航正要离开的路由
- **`next: Function`**: 一定要调用该方法来 **resolve** 这个钩子。执行效果依赖 `next` 方法的调用参数。

```
// 路由守卫
import router from './index'
import { getToken } from '../utils/app'

router.beforeEach((to, from, next) => {
  if (getToken()) {
    // 路由动态添加，分配菜单。每个角色分配不同菜单
    next()
    return
  }
  whiteRouter.includes(to.path) ? next() : next('/login')
})


import cookie from 'cookie_js'

export function getToken() {
  return cookie.get('admin_token')
}
export function setToken(value) {
  cookie.set('admin_token', value)
}
```

```
// 修改vue.config.js
    proxy: {
      '/api': {
        target: 'http://www.web-jshtml.cn/productapi/token', // 加上token
        changeOrigin: true,
        pathRewrite: {
          '^/api': ''
        }
      }
```

```
  actions: {
    login(content, data) {
      return new Promise((resolve, reject) => {
        // 接口
        postLogin(data).then((response) =>{
          let data = response.data
          content.commit('SET_TOKEN', data.token)
          content.commit('SET_USERNAME', data.username)
          setToken(data.token)
          resolve(response)
        }).catch(error => {
          reject(error)
        })
      })
    },
  },
```

```
// 用户切换路由到login页面清除cookie
  if (getToken()) {
    // 路由动态添加，分配菜单。每个角色分配不同菜单
    if (to.path === '/login') {
      removeToken()
      removeUsername()
      store.commit('SET_TOKEN', '')
      store.commit('SET_USERNAM', '')
      next()
    } else {
      next()
    }
    return
  }
```

```
// exit
    exit(content) {
      return new Promise((resolve) => {
        removeToken();
        removeUsername();
        content.commit('SET_TOKEN', '')
        content.commit('SET_USERNAME', '')
        resolve()
      })
    }
    
     const exit =  () => {
       root.$store.dispatch('exit').then(() => {
         root.$router.push({
           name: 'login'
         })
       })
    }
```

## 信息管理UI制作

```
// 表格样式封装
@mixin labelDom($align, $width, $line-height: "normal"){
  label {
    float: left;
    width: $width + px;
    text-align: $align;
    line-height: $line-height + px;
  }
  .warp-content {
      margin-left: $width + px;
  }
}
```

```
// 占位符
<el-col :span="2">&nbsp;</el-col>
// 占位空间
.black-space-30 {
  height: 30px;
}
```

```
// table 样式
// 若修改无效，可以尝试增加权重
.el-table td,
.el-table th {
  text-align: center;
}
```

```
	  // 宽度设置 不要全部写死 留出一行自适应
	  <el-table-column prop="title" label="标题" > </el-table-column>
      <el-table-column prop="catagory" label="类别" width="130"> </el-table-column>
      <el-table-column prop="data" label="日期" width="237"> </el-table-column>
      <el-table-column prop="user" label="管理员" width="115"> </el-table-column>
      <el-table-column label="操作" width="160"> </el-table-column>
```

```
// 底部分页
    <!-- 分页 -->
    <el-row>
      <el-col :span="12">
        <el-button size="medium" @click="deleteAll">批量删除</el-button>
      </el-col>
      <el-col :span="12">
        <el-pagination
          class="pull-right"
          background
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :page-sizes="[2, 4, 6, 8]"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        >
        </el-pagination>
      </el-col>
    </el-row>
```

### 弹窗

```
    <el-dialog title="收货地址" :visible.sync="dialog_info">
      dialog
    </el-dialog>
```

```
// 封装成一个新组件 dialog.vue
```

```
// 子组件修改父组件的值
  watch: {
    flag: {
      handler(newValue, oldValue) {
      // 赋值给一个新变量
        this.dialog_info_flag = newValue
      }
    }
  },
```

```
// messageBox 弹框
```

### vue的全局方法

```
export default {
  install(Vue, options) {
    Vue.prototype.confirm = (data) => {
      Vue.prototype.$confirm(data.content, data.tip || "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: data.type || "warning",
          center: true
        }).then(() => {
          Vue.prototype.$message({
            type: "success",
            message: "删除成功!",
          });
        })
        .catch(() => {
          Vue.prototype.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    }
// 在main.js注入
import global from '@/utils/global.js'
Vue.use(global)
```

```
// vue3.0写法
import { ref } from "@vue/composition-api";
import Vue from 'vue'

export function global3() {
  const str = ref('');
  const confirm = (data) => {
    Vue.prototype.$confirm(data.content, data.tip || "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: data.type || "warning",
        center: true
      }).then(() => {
        data.fn && data.fn(data.id)
        Vue.prototype.$message({
          type: "success",
          message: "删除成功!",
        });
      })
      .catch(() => {
        Vue.prototype.$message({
          type: "info",
          message: "已取消删除",
        });
      });
  }

  return {
    str,
    confirm
  }
}
// 引用后
    const { confirm } = global()
```

## 信息管理模块

```
// /api/news.js
// 分类添加
export function addFirstCategory (data) {
  return service.request({
    method: 'post',
    url: '/news/addFirstCategory/',
    data
  })
}

// 需要验证token信息，在请求头里传入
config.headers.Tokey = cookie.get(admin_token)
```

```
// 优化 添加完信息后，更新视图，需要再次请求资源 可以用vuex解决 减少一次请求
```

```
    const deleteCategoryComfirm = (id) => {
      deleteId.value = id;
      confirm({
        content: "确认删除当前信息，确认后将无法恢复！！",
        tip: "警告",
        fn: delCategory,
        catchFn: () => {}
      });
    };
    const delCategory = () => {
      deleteCategory({ categoryId: deleteId.value })
        .then((res) => {})
        .catch((err) => {});
    };
```

## 接口封装

```
// api/common.js
import { getCategory } from "@/api/news";
import { reactive } from "@vue/composition-api";
export function common() {
  const categoryItem = reactive({
    item: []
  });
  /**
   * 获取分类
   */
  const getInfoCategory = () => {
    getCategory({})
      .then(response => {
        categoryItem.item = response.data.data.data;
      })
      .catch(error => {});
  };

  /**
   * 获取全部分类
   */
  // const getInfoCategoryAll = () => {
  //   GetCategoryAll({})
  //     .then(response => {
  //       categoryItem.item = response.data.data;
  //     })
  //     .catch(error => {});
  // };

  return {
    getInfoCategory,
    // getInfoCategoryAll,
    categoryItem
  };
}
```

```
// 使用vuex
    VuexGetInfoCategory(content, data) {
      return new Promise((resolve, reject) => {
        getCategory()
          .then(res => {
            resolve(res)
          })
          .catch(err => {
            reject(err)
          })
      })
    }
    
    root.$store.dispatch('VuexGetInfoCategory').then(res => {})
```

## 添加接口信息

```
// 数据多时，可以将数据封装在data中
    const data = reactive({
      dialog_info_flag: false,
      formLabelWidth: '70px'
    })
```

### vue子组件修改父组件属性值的两种方法

1、子组件调用`$emit`方法来触发父组件的事件，从而修改属性值

2、使用`.sync`配合$emit方法以update的模式触发事件从而修改父组件属性值

```
// 父组件 传属性title给子组件，使用.sync修饰符
<div>
    <blog :title.sync="title"></blog>
</div>

// 子组件接收父组件传的属性title
props: {
  title: String
}
// 子组件调用$emit方法以update的模式触发事件来修改title的值
<div @click="$emit('update:title','new title')">
  {{title}}
</div>

```

```
          /**
           * 两种刷新数据方式
           * 1、暴力型，直接刷新接口
           * 2、返回列表，手动修改指定的数据
           // let newData = category.item.filter(
          //   (item) => item.id !== deleteId.value
          // );
          // category.item = newData;
           */
```

## info页面编辑详情

```
// router
      {
        path: '/infoDetailed',
        name: 'InfoDetailed',
        hidden: true,
        meta: {
          name: '详情'
        },
        component: () => import('../views/Info/infoDetailed.vue')
      },
```

```
          <el-button type="success" size="mini">
            <router-link :to="{ name: 'InfoDetailed' }"> 编辑详情 </router-link>
          </el-button>
```

```
@for $i from 1 through 110 {
    .margin-left-#{$i} { margin-left:(1px * $i); }
}
```

### 富文本编辑器

```
npm i vue-quill-editor -S
引入
import { quillEditor } from "vue-quill-editor"; 
import 'quill/dist/quill.core.css';
import 'quill/dist/quill.snow.css';
import 'quill/dist/quill.bubble.css';

Template
<quillEditor v-model="form.content" ref="myQuillEditor" :options="data.editorOption"/
```

## 用户管理页面

```
        // 作用域插槽
        <template slot-scope="scope">
          <slot :name="item.slotName" :data="scope.row"></slot>
        </template>
```

```
          // 数据处理
          let requestData = Object.assign({}, data.form); // 浅拷贝
          requestData.role = requestData.role.join(); // 数组转字符串，默认以，号隔开
          requestData.btnPerm = requestData.btnPerm.join(); // 数组转字符串，默认以，号隔开
          requestData.region = JSON.stringify(data.cityPickerData);
```

## 动态路由

1.默认路由(所有人都能访问)

2.动态路由

```
const defaultRouterMap = [{
    path: '/',
    redirect: '/console',
    hidden: true,
    meta: {
      name: '主页'
    }
  },
  {
    path: '/login',
    name: 'login',
    hidden: true,
    meta: {
      name: '登录'
    },
    component: () => import('../views/Login')
  },
  {
    path: '/console',
    name: 'console',
    meta: {
      name: '控制台',
      icon: 'console'
    },
    redirect: '/index',
    children: [{
      path: '/index',
      name: 'index',
      meta: {
        name: '首页'
      },
      component: () => import('../views/Console/index.vue')
    }],
    component: Layout
  },
]
```

```
const asyncRouterMap = [{
    path: '/info',
    name: 'info',
    meta: {
      name: '信息管理',
      icon: 'info'
    },
    component: Layout,
    children: [{
        path: '/infoIndex',
        name: 'infoIndex',
        meta: {
          name: '信息列表'
        },
        component: () => import('../views/Info/index.vue')
      },
      {
        path: '/infoCategory',
        name: 'infoCategory',
        meta: {
          name: '信息分类'
        },
        component: () => import('../views/Info/category.vue')
      },
      {
        path: '/infoDetailed',
        name: 'InfoDetailed',
        hidden: true,
        meta: {
          name: '详情'
        },
        component: () => import('../views/Info/infoDetailed.vue')
      },
    ],
  },
  {
    path: '/user',
    name: 'user',
    meta: {
      name: '用户管理',
      icon: 'user'
    },
    children: [{
      path: '/userIndex',
      name: 'userIndex',
      meta: {
        name: '用户列表'
      },
      component: () => import('../views/User/index.vue')
    }, ],
    component: Layout,
  },
]
```

```
const router = new VueRouter({
  mode: 'hash',
  routes: defaultRouterMap,
  scrollBehavior: () => ({ y: 0 }),
})
```

```
store permission.js
import {
  getUserRole
} from '@/api/login'
import {
  defaultRouterMap,
  asyncRouterMap
} from "@/router/index.js"

function hasPremission(roles, router) {
  if (router.meta && router.meta.role) {
    return roles.some(item => router.meta.role.indexOf(item) >= 0)
  }
}

const state = {
  roles: [],
  allRouters: defaultRouterMap,
  addRouters: []
}

const getters = {
  roles: state => state.roles,
  allRouters: state => state.allRouters,  // 所有的
  addRouters: state => state.addRouters,  // 匹配的
}

const mutations = {
  SET_ROLES(state, value) {
    state.roles = value
  },

  SET_ROUTER(state, router) {
    state.addRouters = router
    state.allRouters = defaultRouterMap.concat(router)
  }
}

const actions = {
  getRoles({
    commit
  }, requestData) {
    return new Promise((resolve, reject) => {
      getUserRole()
        .then(res => {
          let role = res.data
          commit('SET_ROLES', role)
          resolve(role)
        })
        .catch(err => {})
    })
  },
  // "sale", "technician", "manager"
  createRouter({
    commit
  }, data) {
    return new Promise((resolve, reject) => {
      let role = data.role
      let addRouters = []
      // 超管的状态
      if (role.includes('admin')) {
        addRouters = asyncRouterMap
      } else { // 普通管理员
        addRouters = asyncRouterMap.filter(item => {
          if (hasPremission(role, item)) {
            // 优先判断 
            if (item.children && item.children.length > 0) {
              item.children = item.children.filter(child => {
                if (hasPremission(role, child)) {
                  return child;
                }
              })
              return item;
            }
            return item;
          }
        })
        addRouters.push(asyncRouterMap[asyncRouterMap.length - 1]);
      }

      commit('SET_ROUTER', addRouters);
      resolve()
    })
  }
}

export default {
  namespaced: false,
  state,
  getters,
  mutations,
  actions
}
```

```
// 获取用户角色接口
export function getUserRole (data = {}) {
  return service.request({
    method: 'post',
    url: '/userRole/',
    data
  })
}
```

```
router.beforeEach((to, from, next) => {
  if (getToken()) {
    // 路由动态添加，分配菜单。每个角色分配不同菜单
    if (to.path === '/login') {
      removeToken()
      removeUsername()
      store.commit('SET_TOKEN', '')
      store.commit('SET_USERNAME', '')
      next()
    } else {
      // 获取用户的色
      // 动态分配路由权限
      /**
       * 1、什么时候处理动态路由 beforeEach
       * 2、以什么条件处理
       * roles[]
       */
      if (store.getters['roles'].length === 0) {
        store.dispatch('getRoles')
          .then(res => {
            store.dispatch('createRouter', res)
            .then(res => {
              let addRouters = store.getters.addRouters
              let allRouters = store.getters.allRouters
              // 路由更新
              router.options.routes = allRouters
              // 添加动态路由
              console.log(router.options.routes);
              router.addRoutes(addRouters)
              next( {...to, replace:true} )
            })
          })
      } else { next() }
    }
  }
  whiteRouter.includes(to.path) ? next() : next('/login')
})
```

```
// 全局注册方法 按钮权限
import { buttonPermission } from './utils/buttonPermission'
Vue.prototype.btnPerm = buttonPermission

import store from '../store'

buttonPermission.js
export function buttonPermission(permission) {
  const button = store.getters["buttonPermission"];
  return button.indexOf(permission) !== -1; 
}
```

```
// 自定义指令
import Vue from "vue";
import store from "../store/permission.js";
// 自定义指令
Vue.directive("btnPerm", {
  bind: function (el, bingind, vnode) {
    bingind.def.hasBtnPerm()
    // el 绑定的对象 DOM，原生JS处理
    // 操作DOM
    if (bingind.def.hasBtnPerm(bingind.value)) {
      el.className = el.className + " show-button";
    }
  },
  inserted: function (el) {},
  update: function () {},
  componentUpdated: function () {},
  unbind: function () {},
  hasBtnPerm: function () {
    const button = store.getters["buttonPermission"]; // 请求到的数据权限
    const roles = store.getters['roles']; // 获取角色 
    // 如果是超级管理员
    console.log(roles);
    if (roles.includes("admin")) {
      return true
    }
    return button.indexOf(permission) != -1;
  }
})
```

