---
title: vue-mall 商城项目
date: 2021-06-12 16:22:32
tags: vue
categories: 项目总结
---

[vue-mall项目地址](https://github.com/v833/vue-mall) 

### 简介

项目以Vue全家桶作为主要的技术体系，开发网页和交互功能。

```
vue create 'xxx' // 初始化项目
```

### 前端跨域解决方案

跨域是浏览器为了安全做出的限制策略。

浏览器必须请求同源策略：同域名、同协议、同端口

#### CORS跨域

服务端设置，前端直接调用

说明：后台允许前端某个站点进行访问

```
App.vue
<script>
import axios from 'axios'
```

Access-Control-Allow-Credentials: true // 前端跨域发送cookie

#### JSONP跨域

jsonp跨域，前端适配，后台配合 // 前端发callback

说明：前后台同时改造

```
jsonp (url, () => {}) // js 文件
    mounted() {
      let url = 'https://www.imooc.com/search/hotwords';
      jsonp(url, (err,res) => {
        this.data = res
    	})
    },
```

#### 代理跨域

接口代理-通过修改nginx服务器配置来实现

说明：前端修改，后台不动

```
  devServer: {
    host:'localhost',
    port: 8082,
    proxy: {
      // /search/hotwords
      '/api': {
        target: 'https://www.imooc.com/',
        changeOrigin: true,
        pathRewrite: {
          '/api': '' // 将接口设置为空
        }
      }
    }
  }
```

### 需求梳理

熟悉文档，查看原型、读懂需求

了解前端设计稿-设计前端业务架构

了解后台接口文档-指定相关对接规范

协调资源

搭建前端框架

### 搭建

建议大图片放在public， 小图片放在assets (base64)

### 路由封装

```
const routes = [
  {
    path: '/',
    name: 'home',
    component: Home,
    redirect: '/index',
    children: [
      {
        path: 'index',
        name: 'index',
        component: Index
      },
      {
        path: 'product/:id',
        name: 'product',
        component: Product
      },
      {
        path: 'detail/:id',
        name: 'detail',
        component: Detail
      }
    ]
  },
```

### Storage 封装

##### Cookie、localStorage、sessionStorage 三者区别？

都是缓存技术

存储大小：Cookie 4K、Storage 5M

有效期：Cookie 拥有有效期、Storage 永久存储 (localStorage)

Cookie 会发送到服务端，存储在内存中，Storage 只存储在浏览器端

路径：Cookie 有路径限制，Storage 只存储在域名下

API：Cookie 没有特定的API，Storage 有特定的API

document.cookie setItem() localStorag.getItem

##### 为什么要封装Storage，本身已经是API？

Storage 本身有API，但是只是简单的key/value 形式

Storage 只存储字符串，需要人工传换成json对象

Storage 只能一次性清空，不能单个清空

```
// storage 封装

const STORAGE_KEY = 'mall'
export default {
  // 存储值
  setItem (key, value, module_name) {
    if (module_name) {
      let val = this.getItem(module_name)
      val[key] = value
      this.setItem(module_name, val)
    }
    let val = this.getStorage();
    val[key] = value
    window.sessionStorage.setItem(STORAGE_KEY, JSON.stringify(val))
  },
  // 获取某一个模块下面的属性
  getItem (key, module_name) {
    if (module_name) {
      let val = this.getItem(module_name)
      if (val) return val[key]
    }
    return this.getStorage()[key]
  },
  // 获取整个数据
  getStorage () {
    return JSON.parse(window.sessionStorage.getItem(STORAGE_KEY) || '{}')
  },

  // 清空
  clear (key, module_name) {
    let val = this.getStorage()
    if (module_name) {
      delete val[module_name][key]
    } else {
      delete val[key]
    }
    window.sessionStorage.setItem(STORAGE_KEY, JSON.stringify(val))
  }
}
```

### 接口错误拦截

统一报错

未登录统一拦截

请求值、返回值统一处理

```
axios 传参 get 加params post 不加

同时发多个请求？ axios.all([]).then()
```

```
main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import axios from 'axios' // 每个页面都要去写，导入
import VueAxios from 'vue-axios' // 作用域对象挂载在vue实例上

// 根据前端的跨域方式做调整 (接口代理除外)
axios.defaults.baseURL = '/api'

axios.defaults.timeout = 8000; // 超时时间

// 接口错误拦截
axios.interceptors.response.use(function (response) {
  let res = response.data;
  if (res.status == 0) { // 成功
    return res.data
  } else if (res.status == 10) { // 未登录 自定义状态码
    window.location.href = '/#/login'
    // 路由是挂载在vue实例中 这里是js文件 不能用路由跳转
  } else {
    alert (res.msg)
  }
})

Vue.use(VueAxios, axios)
Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

### 接口环境配置

开发上线的不同阶段，需要不同的配置

不同的跨域方式，配置不同

打包的时候统一注入环境参数，统一管理环境，输出不同的版本包

```
"serve": "vue-cli-service serve --mode=development", // 注入参数，把环境变量传给mode
"build": "vue-cli-service build --mode=production",
"test": "vue-cli-service test --mode=test",
```

```
env.js
let baseURL; // 用cors 和 jsons 使用
switch (process.env.NODE_ENV) { // nodejs 的环境变量
  case 'development':
    baseURL = 'http://dev-mall-pre.springboot.cn/api'
    break;
  case 'test': // 不能随便书写 需要写一个.env.(name) 文件 配置NODE_ENV = 'name'
    baseURL = 'http://test-mall-pre.springboot.cn/api'
    break;
  case 'production':
    baseURL = 'http://mall-pre.springboot.cn/api'
    break;
    default:
    baseURL = 'http://mall-pre.springboot.cn/api'
    break

}

export default {
  baseURL
}
```

### Mock 设置

开发阶段，为了高效率，需要提前Mock

减少代码冗余，灵活拔插

减少沟通，减少接口联调时间

##### 三种方案:

1、本地创建json

2、easy-mock 平台

3、集成Mock API

```
 json
 mounted() {
 	// 本地加载请求静态json
    this.axios.get('/mock/user/login.json').then(res => {
      this.res = res // localhost:8080 => 默认在 public 下导入 根目录
    })
  },
```

```
集成Mock API
main.js
const mock = true;// 希望mock开关打开时被拦截，用require而不是import
// import 预编译加载 require 执行加载，如果未需要，不加载
if (mock) {require('./mock/api')}

api.js
import Mock from 'mockjs'
Mock.mock('/api/user/login', data)
// 本地集成mockjs实现数据mock 没有发送真正的请求，代码阶段拦截 
```

### nav-header

给网站设置最小宽度 min-weight

```
安装sass
npm config set registry="https://registry.npmjs.org/"
registry = "https://registry.npm.taobao.org/"
$ set SASS_BINARY_SITE=http://npm.taobao.org/mirrors/node-sass&&npm i node-sass
```

```
导入sass
@import './assets/sass/reset.scss';
```

```
          a {
            display: inline-block;
            width: 110px;
            height: 55px;
            border: 1px solid red;
            &:before { // sass 给a加伪类
              
            }
            cursor: pointer
            设置背景图片
            background: url() no-repeat center;
            background-size: contain
            
<input  autocomplete="off"/> // 历史搜索
```

```
// flex 布局复用
@mixin flex ($hov:space-between, $col: center) {
  display: flex;
  justify-content: $hov;
  align-items: $col;
}
使用
@include flex();
```

```
@mixin bgImg ($w: 0, $h: 0, $img: '', $size: contain) {
  display: inline-block;
  width: $w;
  height: $h;
  background: url($img) no-repeat center;
  background-size: $size;
  margin-right: 4px;
}
```

```
<a target="_blank"></a> // 新页面打开
图片一般自适应展开，要么定义高度，要么定义宽度
```

```
过滤器
  filters: {
    currency (val) {
      if (!val) return '0.00';
      return '￥' + val.toFixed(2) + '元'
    }
  },
  
  {{item.price | currency}} // 使用
```

```
路由跳转 $router.push()
路由取参：$router.params 或者 $router.query
```

### 首页轮播

```
npm install --save vue-awesome-swiper@3.1.3 
对应swiper 4

import { swiper, swiperSlide} from 'vue-awesome-swiper' // swiper组件很大，按需加载
import 'swiper/dist/css/swiper.css'

2 报错  Error in render: "TypeError: Cannot set property 'params' of undefined"  ---跟版本号有关系，4.0 版本首字母大写，3.0版本，首字母小写。
```

```
        <swiper :options="swiperOption">
          <swiper-slide v-for="(item, index) in slideList" :key="index">
            <a href="'/#/product/' + item.id"><img :src="item.img"></a>
          </swiper-slide>
        </swiper>
        分页器
       	  <div class="swiper-pagination"  slot="pagination"></div>
          <div class="swiper-button-prev" slot="button-prev"></div>
          <div class="swiper-button-next" slot="button-next"></div>
          <div class="swiper-scrollbar"   slot="scrollbar"></div>
```

```
      swiperOption: {
        autoplay: true,
        // 循环播放
        loop: true,
        // 切换效果
        effect: 'cube',
        slidesPerView: 3,  // 显示的数目
     	spaceBetween: 30,  // 元素之间的距离
        pagination: {
          el: '.swiper-pagination',
          // 点击分页器切换
          clickable :true,
        },
        navigation: {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev',
        },
      },
```

```
opacity 会把内容全部加透明度
rgba 内容不会有透明度
```

可以用二维数组实现

```
			 <div class="children">
                <ul v-for="(item, index1) in menuList" :key="index1">
                  <li v-for="(sub, index) in item" :key="index">
                    <a :href="sub ? '/#/product/' + sub.id : '' ">
                      <img :src="sub ? sub.img : '/imgs/item-box-1.png'" alt="">
                      {{sub ? sub.name : '小米9'}}
                    </a>
                  </li>
                </ul>
              </div>
```

```
this.phoneList = [res.list.slice(0, 4), res.list.slice(4, 8)] // 赋值
```

### modal组件(transition)

```
  props: {
    // 弹框类型: 小框，中框，大框，表单
    modalType: {
      type: String,
      default: 'form'
    },
    title: String,
    btnType: String, // 按钮类型： 1.确定按钮 2.取消按钮 3.确定取消按钮
    sureText: {
      type: String,
      default: '确定'
    },
    cancelText: {
      type: String,
      default: '取消'
    },
    showModal: Boolean
  },
```

```
@mixin position($pos, $top, $left, $w, $h) {
  position: $pos;
  top: $top;
  left: $left;
  width: $w;
  height: $h
}
```

```
transition 
样式先定义 -active 因为样式会覆盖
```

### 图片懒加载

```
npm i vue-lazyload -S

import VueLazyLoad from 'vue-lazyload'
Vue.use(VueLazyLoad, {
  loading: '/imgs/loading-svg/loading-bars.svg'
})

<img src="item.img" alt=""> => <img v-lazy="item.img" alt="">

// 注意，指令里面的是变量
<img v-lazy="'/imgs/banner-1.png'" alt="">
```

### 登录交互

```
  methods: {
    login () {
      let {username, password} = this // 数据绑定在this上
      this.axios.post('/user/login' {username, password}).then(res => ...)
      this.$cookie.set('userId', res.id, { expires:'1M' })
    }
  }

main.js
Vue.use(VueCookie) // 保存token

单页面应用，刷新会丢失数据，需要在App.vue中重新拉取
Cookie: JSESSIONID=25E86D07E1B121DB1C6B38ECCF6CCADA; userId=2840
// java写的
```

### Vuex

vue 中数据是单项流通的，如果 A 与 B 之间没有任何关系，而A想用B的变量。兄弟组件传递数据。

```
vuex state 中的数据不具有响应式， state中的数据发生变化，不会主动通知调用该数据的对象，我们可以用computed监听数据
```

### 产品站参数

#### 吸顶功能

```
  mounted() {
    window.addEventListener('scroll', this.initHeight) // 方法抽离出来，为了移除事件使用，使用function() 事件在组件销毁时不会移除
  },
  
  data () {
  	return {
    	isFixed: false
    }
  },
  mounted() {
    window.addEventListener('scroll', this.initHeight)
  },
  methods: {
    initHeight () {
      let scrollTop = window.scrollY || document.documentElement.scrollTop || document.scrollTop; // 兼容浏览器
      console.log(scrollTop);
      this.isFixed = scrollTop > 152
    }
  }
   destroyed() {
    window.removeEventListener('scroll', this.initHeight, false)
  },
```

```
box-shadow:X轴偏移量、Y轴偏移量、模糊半径、扩散半径和颜色。
```

### video

```
<div class="overlay"></div> // 遮罩
<video src="/imgs/product/video.mp4" muted autoplay controls="controls"></video>
设置自动播放 需要先数值muted    loop
```

```
object-fit:cover // 类似backgroundimg 这个CSS属性可以达到最佳最完美的居中自动剪裁图片的功能。
background-size只能作用于有背景图的，
object-fit可以作用于类似 img, video等的标签。
```

```
    closeVideo () {
      this.showSlide = 'slideUp';
      setTimeout (() => {
        this.showSlide = false
      }, 600)
    },
```

```
1.router是VueRouter的一个对象,通过Vue.use(VueRouter)和VueRouter构造函数得到一个router的实例对象,这个对象中是一个全局的对象,他包含了所有的路由包含了许多关键的...
2.route是一个跳转的路由对象,每一个路由都会有一个route对象,是一个局部的对象,可以获取对应的name,path,params,query等 我们可以从vue devtools中看到每个路由对象的不同.当前的路由信息，包含了当前 URL 解析得到的信息。包含当前的路径，参数，query对象等。
```

### 商品详情页面

```
浮动及清除浮动
.fl{
  float: left;
}
.fr{
  float: right;
}
.clearfix:before,.clearfix:after{
  content:' ';
  display:table;
}
.clearfix:after{
  clear: both;
}

<div class="container clearfix"> 父元素添加清除浮动
```

### 购物车页面

```
@import './'  // 不带./默认是插件 去node_modules中查找
```

数据一般放在后台，防止前端篡改数据

```
      // 公共赋值
      renderData(res){
        this.list = res.cartProductVoList || [];
        this.allChecked = res.selectedAll;
        this.cartTotalPrice = res.cartTotalPrice;
        this.checkedNum = this.list.filter(item=>item.productSelected).length;
      }, // 某项数据改变后，与其相关联的数据不变化，需要重新赋值
```

### Element UI

```
<a> 优先级高于class
<h2> 优先级高于class
```

主要用于B端

```
import { Message } from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.prototype.$message = Message;
```

### babel

`babel.config.js`和 `.babelrc `不会被合并

js.map 源码存放 上线后删除

```
  "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ] // 按需加载
```

### 退出登录

因为是单页面，所以把数据存储在Vuex中，Vuex中的数据存储在内存，页面刷新后，内存中的数据自动消失。

```
        this.$router.push({
          path: '/index',
          query: {
            from: 'login'
          }
        }) // query传参
        
         this.$router.push({
          name: 'index',
          params: {
            from: 'login'
          }
        }) // params 传参，用 name
        
   if (this.$route.params && this.$route.params.from == 'login')
    this.getCartCount()
  },
```

```
    if (this.$cookie.get('userId')) {
      this.getUser();
      this.getCartCount()
    } // 如果未登录 不需要调取登录信息 判断 cookie 
    清除cookie this.$cookie.set('xx', '', {expires: -1})
    expires 和后端保持一致 登录有效期
    会话级别：Session 把整个浏览器关闭coookie消失
```

### 订单确认

```
  mounted() {
    let path = this.$route.path;
    if (path == '/order/confirm') {
      this.title = "确认订单"
      this.tip = "请认真填写收获地址"
    } else if (path == 'order/list') {
      this.title = '订单列表'
      this.tip = "请谨防钓鱼链接或诈骗电话"

    } else if (path == '/order/pay') {
      this.title = '订单支付'
      this.tip = "请谨防钓鱼链接或诈骗电话"
    }
  },
```

### 地址

```
	// axios的封装
   submitAddress() {
      let { checkedItem, userAction } = this;
      let method,
          url,
          params = {};
      if (userAction == 0) {
        (method = "post"), (url = "/shippings");
      } else if (userAction == 1) {
        (method = "put"), (url = `/shippings/${checkedItem.id}`);
      } else {
        (method = "delete"), (url = `/shippings/${checkedItem.id}`);
      }
      if (userAction == 0 || userAction == 1) {
        let {
            receiverName,
            receiverMobile,
            receiverProvince,
            receiverCity,
            receiverDistrict,
            receiverAddress,
            receiverZip
            } = checkedItem;
        let errMsg = "";
        if (!receiverName) {
          errMsg = "请输入收货人名称";
        } else if (!receiverMobile || !/\d{11}/.test(receiverMobile)) {
          errMsg = "请输入正确格式的手机号";
        } else if (!receiverProvince) {
          errMsg = "请选择省份";
        } else if (!receiverCity) {
          errMsg = "请选择对应的城市";
        } else if (!receiverAddress || !receiverDistrict) {
          errMsg = "请输入收货地址";
        } else if (!/\d{6}/.test(receiverZip)) {
          errMsg = "请输入六位邮编";
        }
        if (errMsg) {
          this.$message.error(errMsg);
          return;
        }
        params = {
          receiverName,
          receiverMobile,
          receiverProvince,
          receiverCity,
          receiverDistrict,
          receiverAddress,
          receiverZip
        };
      }
      this.axios[method](url, params).then(() => {
        this.closeModal();
        this.getAddressList();
        this.$message.success("操作成功");
      });
    },
```

```
/\d{11}/.text(arg) 
```

### 订单支付

```
    paySubmit(payType){
      if(payType == 1){
        window.open('/#/order/alipay?orderId='+this.orderId,'_blank');
        // 可以打开新窗口 window.open(URL,name,specs,replace) specs: '_blank&_self'
      }else{
        this.axios.post('/pay',{
          orderId:this.orderId,
          orderName:'Vue高仿小米商城',
          amount:0.01,//单位元
          payType:2 //1支付宝，2微信
        }).then((res)=>{
          QRCode.toDataURL(res.content)
          .then(url => {
            this.showPay = true;
            this.payImg = url;
            this.loopOrderState();
          })
          .catch(() => {
            this.$message.error('微信二维码生成失败，请稍后重试');
          })
        })
      }
    },
```

```
    paySubmit () {
      this.axios.post('/pay', {
        orderId: this.orderId,
        orderName: 'Vue高仿小米',
        amount: 0.01,
        payType: 1
      }).then((res) => {
        this.content = res.content
        setTimeout (() => { document.forms[0].submit() }, 100) // 提交表单
      })
    }
```

### 支付对接

调用后端接口，

传入订单id，商品名称，金额，支付类型，

得到表单 通过v-html赋值 通过document.forms[0].submit() 提交 (做一个中间页面)

```
          QRCode.toDataURL(res.content)
          .then(url => { // 转换成base64
            this.showPay = true;
            this.payImg = url;
            this.loopOrderState();
          })
          .catch(() => {
            this.$message.error('微信二维码生成失败，请稍后重试');
          })
```

```
    // 轮询当前订单支付状态
    loopOrderState(){
      this.T = setInterval(()=>{
        this.axios.get(`/orders/${this.orderId}`).then((res)=>{
          if(res.status == 20){
            clearInterval(this.T);
            this.goOrderList();
          }
        })
      },1000);
    }, // 如果用户已经支付，自动跳转
```

```
axios.interceptors.response.use(function (response) {
  let res = response.data;
  let path = location.hash;
  if (res.status == 0) { // 成功
    return res.data
  } else if (res.status == 10) { // 未登录 自定义状态码
    if (path !== '#/index') { // 处于首页不需要跳转login
      window.location.href = '/#/login'
      // 路由是挂载在vue实例中 这里是js文件 不能用路由跳转
    }
  } else {
    Message.warning(res.msg)
    return Promise.reject(res) // 不抛出认为是成功状态
  }
}, (error) => { // 状态码拦截 请求发送失败
  let res = error.response;
  Message.error(res.data.message)
  return Promise.reject(res)
})
```

### 订单列表

```
  import { Pagination,Button } from 'element-ui'
  import infiniteScroll from 'vue-infinite-scroll'
```

```
    <el-pagination
            v-if="false"
            class="pagination"
            background
            layout="prev, pager, next"
            :pageSize="pageSize"    // :pageSize="pageSize"
            :total="total"
            @current-change="handleChange"
            >
          </el-pagination>
    components:{
      OrderHeader,
      Loading,
      NoData,
      [Pagination.name]:Pagination, // 使用方法
      [Button.name]:Button
    },
```

```
      // 第一种方法：分页器
      handleChange(pageNum){
        this.pageNum = pageNum;
        this.getOrderList();
      },    // 向右：添加text-align: right
```

```
     // 第二种方法：加载更多按钮
    loadMore(){
        this.pageNum++;
        this.getOrderList();
      },
```

```
      // 第三种方法：滚动加载，通过npm插件实现
      npm i infinite-scroll 是一个指令
       directives: {
       	infiniteScroll
       }
       
          <div class="scroll-more"
            v-infinite-scroll="scrollMore"
            infinite-scroll-disabled="true"
            infinite-scroll-distance="410"
            v-if="true"
          >
            <img src="/imgs/loading-svg/loading-spinning-bubbles.svg" alt="" v-show="loading">
          </div>

      scrollMore(){
        this.busy = true;
        setTimeout(()=>{
          this.pageNum++;
          this.getList();
        },500);
      },
      // 专门给scrollMore使用
      getList(){
        this.loading = true;
        this.axios.get('/orders',{
          params:{
            pageSize:10,
            pageNum:this.pageNum
          }
        }).then((res)=>{
          this.list = this.list.concat(res.list);
          this.loading = false;
          if(res.hasNextPage){
            this.busy=false;
          }else{
            this.busy=true;
          }
        });
```

### 优化

```
component: () => import('../pages/product.vue') // 预加载，文件加载进html，等待空闲时生成js文件。
实现真正按需加载：
    chainWebpack: (config) => {
      config.plugins.delete('prefetch')
    }
```

