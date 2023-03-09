---
title: 理解 ES6 的 Map 数据结构
date: 2021-06-14 22:52:08
tags: javascript
swiper: true
swiperDesc: 'ES6 新增了一种数据结构 Map，与传统的对象字面量类似，它的本质是一种**键值对的组合**。但是与对象字面量不同的是，对象字面量的键只能是**字符串**，对于非字符串类型的值会采用强制类型转换成字符串，而 Map 的键却可以由各种类型的值组成。
'
categories: 前端
---

{% title h2, Map %}
ES6 新增了一种数据结构 Map，与传统的对象字面量类似，它的本质是一种**键值对的组合**。但是与对象字面量不同的是，对象字面量的键只能是**字符串**，对于非字符串类型的值会采用强制类型转换成字符串，而 Map 的键却可以由各种类型的值组成。

```js
// 传统的对象类型
let xx = {name: 'xx'}
let obj = {}
obj[xx] = '123' // {[object Object]: "123"}
alert( obj["[object Object]"] ) // 123
```
在上面案例中，采用的是传统的对象处理方式，我们将对一个对象作为对象的键，在输出时会将所有的键如 xx  转换为字符串。所以我们得到字符串键 "[object Object]"。这显然不是我们想要的结果。
```js
let xx = {name: 'xx'}
let map = new Map()
map.set(xx, 123) // Map(1) {{…} => 123}
alert( map.get(xx) ) // 123
```
使用 Map 处理方法，将一个对象作为键添加到 map 实例中，在输出时发现，键的值为对象的真实值，并没有转换成字符串。

当创建一个 Map 后，我们可以传入一个带有键值对的数组（或其它可迭代对象）来进行初始化。

```js
let map = new Map([
  ['name','xx'],
  ['age', 23]
])
console.log(map) // Map(2) {"name" => "xx", "age" => 23}
```

{% title h2, Map 的属性和方法 %}
- new Map() —— 创建 map。
- map.set(key, value) —— 根据键存储值。
- map.get(key) —— 根据键来返回值，如果 map 中不存在对应的 key，则返回 undefined。
- map.has(key) —— 如果 key 存在则返回 true，否则返回 false。
- map.delete(key) —— 删除指定键的值。
- map.clear() —— 清空 map。
- map.size —— 返回当前元素个数。
> map[key] 不是使用 Map 的正确方式
>
> 虽然 map[key] 也有效，例如我们可以设置 map[key] = 23，这样会将 map 视为 JavaScript 的 plainobject，因此它暗含了所有相应的限制（没有对象键等）。
> 所以我们应该使用 map 方法：set 和 get 等。

在 Map 数据结构中，所有的键都必须具有**唯一性**。如果对同一个键进行多次赋值，那么后面的值会覆盖前面的值。

```js
let map = new Map()
map.set(1, 'aa')
map.set(1, 'bb')
map.get(1) // 'bb'
```
> Map 是怎么比较键的？
>
> Map 使用 SameValueZero 算法来比较键是否相等。它和严格等于 === 差不多，但区别是 NaN 被看成是等于 NaN。所以 NaN 也可以被用作键。

>链式调用
>
>每一次 map.set 调用都会返回 map 本身，所以我们可以进行“链式”调用：
>
>map.set('name', 'xx1').set('age', 23)

{% title h2, Map 的遍历 %}
- map.keys() —— 遍历并返回所有的键，
- map.values() —— 遍历并返回所有的值，
- map.entries() —— 遍历并返回所有的实体[key, value]，**for..of 在默认情况下使用的就是这个**，
- map.forEach(item, key) —— 第一个参数表示值，第二个参数表示键。

```js
let map = new Map()
map.set('name', 'xx')
map.set('age', 23)

map.forEach((value, key) => {
  console.log(value, key); 
  // xx name 
  // 23 age
})

for (let key of map.keys()) {
  console.log(key);
  // name
  // age
}

for (let value of map.values()) {
  console.log(value);
  // xx
  // 23
}

for (let [key, value] of map.entries()) {
  console.log(key, value);
    // name xx
    // age 23
}

```
> map.entries === map[Symbol.iterator]

>使用插入顺序
>
>迭代的顺序与插入值的顺序相同。与普通的 Object 不同，Map 保留了此顺序。


{% title h2, Map 与其他数据类型的转换 %}

{% title h3, 数组转换成 Map , warning %}
数组转换成 Map，可以通过 Map 构造函数实现，使用 new 操作符生成 Map 实例。

```js
let arr = [['name', 'xx'], ['age', 23]]

let map = new Map(arr)
console.log(map) // Map(2) {"name" => "xx", "age" => 23}
```

{% title h3, Map 转换成数组 , warning %}
Map 转换成数组，可以通过扩展运算符实现。

```js
let map = new Map()
map.set('name', 'xx')
map.set('age', 23)

let arr = [...map]
console.log(arr) // (2) [Array(2), Array(2)]
```

{% title h3, 对象转换成 Map , warning %}
如果我们想从一个已有的普通对象来创建一个 Map，那么我们可以使用内建方法 Object.entries(obj)，该方法返回对象的键/值对数组，该数组格式完全按照 Map 所需的格式。

```js
let obj = {
  'name': 'xx',
  'age': 23
}

let map = new Map(Object.entries(obj))
console.log(map) // Map(2) {"name" => "xx", "age" => 23}
```

{% title h3, Map 转换成对象 , warning %}
我们刚刚已经学习了如何使用 Object.entries(obj) 从普通对象创建 Map。

Object.fromEntries 方法的作用是相反的：给定一个具有 [key, value] 键值对的数组，它会根据给定数组创建一个对象。

```js
let map = new Map()
map.set("name", 'xx')
map.set("age", 23)

let obj = Object.fromEntries(map)
console.log(obj) // {name: "xx", age: 23}
```

{% title h3, Map 转换成 Json , warning %}

{% title h4, 当 Map 的键名都是字符串 , red %}
当 Map 的键名都是字符串时，可以先将 Map 转换成对象，然后调用JSON.stringify()函数。

```js
let map = new Map()
map.set("name", 'xx').set("age", 23)

let json = JSON.stringify(Object.fromEntries(map))
console.log(json) //{"name":"xx","age":23}
```

{% title h4, 当 Map 的键名有非字符串 , red %}
当 Map 的键名有非字符串时，我们可以先将 Map 转换成数组，然后调用JSON.stringify()函数

```js
let map = new Map()
map.set({fn: 'foo'}, 'xx').set(true, 'abc')

let json = JSON.stringify([...map])
console.log(json) // [[{"fn":"foo"},"xx"],[true,"abc"]]
```