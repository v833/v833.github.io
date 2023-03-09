---
title: React的使用
date: 2021-07-12 16:21:52
tags: React
categories: 学习笔记
---

## React 的三大特性：

- `数据驱动 -> 单向数据流`
- `函数式编程 = 组件化 + JSX`
- `虚拟 DOM -> 跨平台`

#### setState是同步还是异步？

**异步**npm

可能会执行多次setState

无法规定、限制用户如何使用setState

没必要每次setState都要重新渲染，考虑性能

即便是每次重新渲染，用户也看不到中间的效果(js单线程)异步

**setState的过程**

每个组件都有一个renderComponent方法

执行renderComponent会重新执行实例的render

patch(oldVnode, newVnode)

## React的基本使用

### 事件

#### bind this

this默认是undefined 需要改变this指向

```
this.handler = this.handler.bind(this)
```

也可以使用箭头函数

#### 关于event参数

```
  clickHandler2 = (e) => {
    console.log(e);
    e.nativeEvent(); // 原生event 所有的事件挂载到document
    e.preventDefault();
    e.stopPropagation();
    console.log(e.target); // 指向当前元素, 即当前元素触发 
    console.log(e.currentTarget); // 指向当前元素，假象！  null
    // 注意，event其实是React封装的。可以看__proto__.constructor 是 SyntheticBaseEvent 组合事件
    // 原生event的__proto__.constructor是MouseEvent
  }
```

### 表单

#### 受控组件

input textarea select用value

checkbox radio 用checked

```
      <div>
        <label htmlFor="inputName">Name</label>
        <input id="inputName" value={this.state.name} onChange={this.changeHandle}></input>
        <p>{this.state.name}</p>
      </div>
```

#### 非受控组件

**ref**

**defaultValue defaultChecked**

**手动操作DOM元素**

##### 使用场景

必须手动操作DOM元素，setState实现不了

文件上传 <input type="file">

某些富文本编辑器，需要传入DOM元素

```
import React, { Component } from "react";

export class UncontrolledDemo extends Component {
  constructor(props) {
    super(props)
    this.state = {
      name: 'xx'
    }
    this.nameInputRef = React.createRef()
  }
  render() {
    return (
      <div>
        {/* 没有onChange事件
        state 并不会随着变化 */}
        <input defaultValue={this.state.name} ref={this.nameInputRef}></input>
        <p>{this.state.name}</p>
        <button onClick={this.alertName}>click me</button>
      </div>
    )
  }
  alertName = () => {
    console.log(this.nameInputRef);
    const e = this.nameInputRef.current // 通过ref获取DOM节点
    alert(e.value)
  }
}
```

### 父子组件通讯

props传递数据

props传递函数

### setState

#### 不可变值(函数式编程，纯函数)

#### 可能是异步更新

#### 可能会被合并

```
import { Component } from "react";

export class StateDemo extends Component {
  constructor(props) {
    super(props)
    // state 要定义在构造函数中
    this.state = {
      count: 0
    }
  }
  clickHandle = () => {
    // 不要直接修改state，使用不可变数据
    this.setState({
      count: this.state.count + 1
    }, () => {
      console.log(this.state.count); // 回调函数
    })
    // setTimeout 中setState是同步的
    // setTimeout(() => {
    //   this.setState({
    //     count: this.state.count + 1
    //   })
    //   console.log(this.state.count);
    // })
    // 自定义DOM事件，setState同步
    
    console.log(this.state.count); // 异步，拿不到最新值
  }
  // 操作数组时、对象的常用形式
  // this.setState({
  //   list1: [...this.state.list, 100], // 追加
  //   list2: this.state.list.slice(0, 3), // 截取
  //   list3: this.state.list.filter(item => item > 10),
  // })
  // this.setState({
  //   obj1: Object.assign({}, this.state.obj, {a: 100}),
  //   obj2: {...this.state.obj, a: 100}
  // })
  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button onClick={this.clickHandle}>click me</button>
      </div>
    )
  }
}
```

### 组件生命周期

**装载阶段**

constructor componentDidMount

**更新阶段**

render shouldComponentUpdate componentDidUpdate

**卸载阶段**

componentWillUnmount

### 函数组件

```
function List(props) {
	const { list } = this.props
	return ()
}
纯函数，输入props，输出JSX
没有实例，没有生命周期，没有state
不能扩展其他方法
```

### Portals

组件默认会按照既定层次嵌套渲染

让组件渲染到父组件以外

```
  render() {
    return ReactDOM.createPortal(
      <div className="modal">{this.props.children}</div>,
      document.body
    )
  } // 使用Portals渲染到body上，fixed元素要放在body上，有更好的浏览器兼容性
```

##### 使用场景

父组件设置了overflow：hidden

父组件z-index值太小

fixed元素要放在body第一层级

### context

公共信息传递给每个组件 非父子组件通讯

```
import React, { Component } from "react";

const ThemeContext = React.createContext('light')

export class ContextDemo extends Component {
  constructor(props) {
    super(props)
    this.state = {
      theme: 'light'
    }
  }
  render() {
    return (
      <ThemeContext.Provider value={this.state.theme}>
        <Toolbar />
        <div>{this.state.theme}</div>
        <button onClick={this.changeTheme}>click me</button>
      </ThemeContext.Provider>
    )
  }
  changeTheme = () => {
    this.setState({
      theme: this.state.theme === 'light' ? 'dark' : 'light'
    })
  }
}

class Toolbar extends Component {
  static contextType = ThemeContext // es6 写法
  render() {
    const theme = this.context // React会向上找最近的theme provider
    return (
      <div>
        <div>123{theme}</div>
        <ThemeLink />
      </div>
    )
  }
}
// Toolbar.contextType = ThemeContext // 指定conteType

function ThemeLink (props) {
  return (
    <ThemeContext.Consumer>
      {value => value}
    </ThemeContext.Consumer>
  )
} // 函数组件写法
```

### 异步组件

import()

React.lazy

React.Suspense

```
const ContextDemo  = React.lazy(() => import('./components/todo/Demo/ContextDemo'))

      <React.Suspense fallback={<div>loading</div>}>
      <ContextDemo />
      </React.Suspense>
```

### 性能优化

shouldComponentUpdate(SCU)

PureComponent 和 React.memo

不可变值 immutable.js

```
shouldComponentUpdate(nextProps, nextState) {
	if (nextState.count !== this.state.count) {
	return true // 可以渲染
	}
	return false // 不可渲染
}
// 默认父组件更新，子组件无条件更新
// SCU一定要每次都用吗? 需要的时候才优化
// SCU一定要配合不可变值
```

```
PureComponent ,SCU中实现了浅比较
memo函数组件中的PureComponent
```

```
class Pure extends React.PureComponent {}
export React.memo(MyComponent, ateEqual)
```

```
immutable.js
彻底拥抱不可变值
基于共享数据(不是深拷贝)，速度快
```

### 高阶组件

#### 关于组件公共逻辑的抽离

##### 高阶组件HOC

```
// 高阶组件并不是一种功能，而是一种模式
const HOCFactory = (Component) => {
	const HOC extends React.Component {
	//在此处定义多个组件的公共逻辑
		render() {
			return <Component {...this.props} /> // 返回拼装的结果
		}
		return HOC
	}
}
const EnhancedComponent1 = HOCFactory(WrappedComponent1)
const EnhancedComponent2 = HOCFactory(WrappedComponent2)
```

```
const withMouse = (Component) => {
  class withMouseComponent extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        x: 0,
        y: 0
      }
    }
    handleMouseMove = (e) => {
      console.log(e);
      this.setState({
        x: e.clientX,
        y: e.clientY
      })
    }
    render() {
      return ( <div style = {{height: '500px'}} onMouseMove={this.handleMouseMove} > 
      {/* 传递所有props 增加mouse属性 */ }
          <Component {...this.props} mouse={this.state} /> 
          </div>
      )
    }
  }
  return withMouseComponent
}
const App = (props) => {
  const {x, y} = props.mouse
  return (
    <div style={{height: '500px'}}>
      <h1>{x}, {y}</h1>
    </div>
  )
}
export default withMouse(App)
```

##### Render Props

```
// Render Props 的核心思想
// 通过一个函数将class组件的state作为props传递给纯函数组件
class Factory extends React.component {
	constructor(props) {
		super(props)
		this.state = {
			// state 即多个组件的公共逻辑的数据
		}
	}
	// 修改state
	render() {
		return <div>{this.props.render(this.state)}</div>
	}
}

const App = () => {
	<Factory render={
	// render 是一个函数组件
	(props)) => <P> {props.a} {props.b} </p>
	}
}
```

```
import { Component } from "react";
class Mouse extends Component {
  constructor(props) {
    super(props)
    this.state = {x: 0, y: 0}
  }
  handleMouseMove = (e) => {
    this.setState({
      x: e.clientX,
      y: e.clientY
    })
  }
  render() {
    return (
      <div style={{height: '500px'}} onMouseMove={this.handleMouseMove}>
        {/* 将当前state作为props，传递给render(render是一个纯函数) */}
        {this.props.render(this.state)}
      </div>
    )
  }
}
const App = (props) => (
  <div style={{height: '500px'}}>
    <p>{props.a}</p>
    <Mouse render={({x, y}) => <h1> {x}, {y} </h1>} />
  </div>
)
export default App
```

### Redux的使用

#### 三大原则

##### 单一数据源

整个应用的 state被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个store中。

##### State 是只读的

唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。

##### 使用纯函数来执行修改

为了描述 action 如何改变 state tree ，你需要编写 reducer

#### 单项数据流

dispatch(action)

reducer => newState

subscribe 触发通知

#### react-redux

< Provider> 

mapStateToProps mapDispatchToProps

#### 异步action

```
// 同步action
export const addTodo = text => {
	return {
		type: 'x',
		text
	}
}

// 异步action
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import rootReducer from './reducers/index'
const store = createStore(rootReducer, applyMiddleware(thunk))
export const addTodoAsync = text => {
	return (dispatch) => {
		fetch(url).then(res => dispatch(addTodo(res.text)))
	}
}
```

```
button(callback) => dispatch(action) => reducer(state) => view
```

#### redux数据流图

![img](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimage.mamicode.com%2Finfo%2F201912%2F20191203142453280400.png&refer=http%3A%2F%2Fimage.mamicode.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1628914849&t=d2e6599519536f4b2e22059cde1102f2)

## React原理

### 函数式编程

纯函数

不可变值

### 合成事件

所有事件挂载到document

event不是原生的，是SyntheticEvent合生事件对象

更好的兼容性和跨平台

### setState batchUpdate

有时异步(普通使用)，有时同步(setTimeout, DOM事件)

有时合并(对象模式)，有时不合并(函数模式)

setState看是否命中batchUpdate 判断isBatchingUpdates

### 组件之间如何通讯？

父子组件props

Redux Context

### JSX本质

createElement

执行返回vnode

### shouldComponentUpdate用途

性能优化

配合不可变值，一起使用，否则会出错

### 纯函数

返回一个新值，没有副作用

重点：不可变值

如arr1 = arr,slice()

### 函数组件和class组件的区别

纯函数，输入props，输出JSX

没有实例，没有生命周期，没有state

不能扩展其他方法

### 什么是受控组件？

表单的值，受state控制

需要自行监听onChange事件，更新state

对比非受控组件

### 多个组件有公共逻辑，如何抽离？

高阶组件

Render Props

### PureComponent

实现了浅比较的shouldComponentUpdate

优化性能

但要结合不可变值使用

### React性能优化

渲染列表加key

自定义事件、DOM事件及时销毁

合理使用异步组件

减少函数bind this的次数

合理使用SCU PureComponent和memo

图片懒加载