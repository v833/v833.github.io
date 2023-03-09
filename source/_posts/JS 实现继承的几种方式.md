---
title: JS 实现继承的几种方式 
date: 2021-06-19 13:37:52
tags: javascript
swiper: true
swiperDesc: '原型链继承
</br>构造函数继承
</br>组合继承
</br>寄生组合式继承
'
top: true
categories: 前端
---
>**继承**作为面向对象语言的三大特性之一，可以在不影响父类对象实现的情况下，使得子类对象具有父类对象的特性；同时还能再不影响父类对象行为的情况下扩展子类对象独有的特性，为编码带来了极大的便利。
>下面我们就来看看 `JavaScript` 中都有哪些实现继承的方法。

{% title h2, 原型链继承 %}
原型链继承是比较常见的继承方式之一，其中涉及的构造函数、原型和实例，三者之间存在着一定的关系，即**每一个构造函数都有一个原型对象，原型对象又包含一个指向构造函数的指针，而实例则包含一个原型对象的指针。**
>原型链继承的主要思想是：**重写子类的prototype属性，将其指向父类的实例**。
>下面我们结合代码来了解一下。

```js
function Animal (name) {
  // 属性
  this.name = name
  this.type = 'Animal'
  // 实例函数
  this.sleep = function () {
    console.log(this.name + '正在睡觉');
  }
}
// 原型函数
Animal.prototype.eat = function (food) {
  console.log(`${this.name}正在吃${food}`);
}

// 子类
function Cat (name) {
  this.name = name
}
// 原型继承
Cat.prototype = new Animal()
// 将Cat的构造函数指向自身
Cat.prototype.constructor = Cat

let cat = new Cat('Tom')
console.log(cat.name) // Tom
console.log(cat.type) // Animal
cat.sleep() // Tom正在睡觉
cat.eat('猫罐头') // Tom正在吃猫罐头
```
在子类`Cat`中，我们没有增加`type`属性，因此会直接继承父类`Animal`的`type`属性。

在子类`Cat`中，我们增加了`name`属性，在生成子类实例时，`name`属性会覆盖父类`Animal`属性值。

同样因为`Cat`的`prototype`属性指向了`Animal`类型的实例，因此在生成实例`Cat`时，会继承实例函数和原型函数。
>需要注意：
>
>Cat.prototype.constructor = Cat
>
>如果不将Cat原型对象的constructor属性指向自身的构造函数，那将指向父类Animal的构造函数。

{% title h3, 原型链继承的优点 , warning %}

{% title h4, 简单，易于实现 , red %}
只需要设置子类的`prototype`属性指向父类的实例即可。

{% title h4, 可通过子类直接访问父类原型链属性和函数 , red %}

{% title h3, 原型链继承的缺点 , warning %}

{% title h4, 子类的所有实例将共享父类的属性 , red %}
子类的所有实例将共享父类的属性会带来一个很严重的问题，父类包含**引用值**时，子类的实例改变该引用值会在所有实例中共享。

```js
function Animal () {
  this.skill = ['eat', 'jump', 'sleep']
}
function Cat () {}
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat

let cat1 = new Cat()
let cat2 = new Cat()
cat1.skill.push('walk')
console.log(cat1.skill) // ["eat", "jump", "sleep", "walk"]
console.log(cat2.skill) // ["eat", "jump", "sleep", "walk"]
```

{% title h4, 在子类实例化时，无法向父类的构造函数传参 , red %}
在通过`new`操作符创建子类的实例时，会调用子类的构造函数，而在子类的构造函数中并没有设置与父类关联，从而导致无法向父类的构造函数传递参数。

{% title h4, 无法实现多继承 , red %}
子类的`prototype`只能设置一个值，设置多个值时，后面的值会覆盖前面的值。

{% title h2, 构造函数继承（借助 call） %}
>构造函数继承的主要思想：**在子类的构造函数中通过call()函数改变thi的指向，调用父类的构造函数，从而将父类的实例的属性和函数绑定到子类的this上。**

```js
  // 父类
function Animal (age) {
  // 属性
  this.name = 'Animal'
  this.age = age
  // 实例函数
  this.sleep = function () {
    console.log(this.name + '正在睡觉');
  }
}
// 原型函数
Animal.prototype.eat = function (food) {
  console.log(`${this.name}正在吃${food}`);
}
function Cat (name) {
  // 核心，通过call()函数实现Animal的实例的属性和函数的继承
  Animal.call(this)
  this.name = name
}

let cat = new Cat('Tom')
cat.sleep() // Tom正在睡觉
cat.eat() // Uncaught TypeError: cat.eat is not a function
```
通过代码可以发现，子类可以正常调用父类的实例函数，而无法调用父类原型上的函数，**这是因为子类并没有通过某种方式来调用父类原型对象上的函数**。

{% title h3, 构造继承的优点 , warning %}

{% title h4, 解决了子类实例共享父类属性的问题 , red %}
`call()`函数实际时改变父类`Animal`构造函数中`this`的指向，然后调用`this`指向了子类`Cat`，相当于将父类的属性和函数直接绑定到了子类的`this`中，成了子类实例的熟属性和函数，因此生成的子类实例中是各自拥有自己的属性和函数，不会相互影响。

{% title h4, 创建子类的实例时，可以向父类传参 , red %}

```js
   // 父类
function Animal (age) {
  this.name = 'Animal'
  this.age = age
}
function Cat (name, parentAge) {
  // 在子类生成实例时，传递参数给call()函数，间接地传递给父类，然后被子类继承
  Animal.call(this, parentAge)
  this.name = name
}

let cat = new Cat('Tom', 10)
console.log(cat.age)
```
{% title h4, 可以实现多继承 , red %}

在子类的构造函数中，可以多次调用`call()`函数来继承多个父对象。

{% title h3, 构造函数的缺点 , warning %}

{% title h4, 实例只是子类的实例，并不是父类的实例 , red %}
因为我们并未通过原型对象将子类与父类进行串联，所以生成的实例与父类并没有关系。

{% title h4, 只能继承父类实例的属性和函数，并不能继承原型对象上的属性和函数 , red %}
与上面原因相同。

{% title h4, 无法复用父类的构造函数 , red %}
因为父类的实例函数将通过`call()`函数绑定到子类的`this`中，因此子类生成的每个实例都会拥有父类实例的引用，这会造成不必要的内存消耗，影响性能。

{% title h2, 组合继承 %}
>组合继承的主要思想：**结合构造继承和原型继承的两种方式，一方面在子类的构造函数中通过call()函数调用父类的构造函数，将父类的实例的属性和函数绑定到子类的this中；另一方面，通过改变子类的prototype属性，继承父类的原型对象上的属性和函数。**

```js
// 父类
function Animal (age) {
  // 实例属性
  this.name = 'Animal'
  this.age = age
  this.skill = ['eat', 'jump', 'sleep']
  // 实例函数
  this.sleep = function () {
    console.log(this.name + '正在睡觉')
  }
}
// 原型函数
Animal.prototype.eat = function (food) {
  console.log(`${this.name}正在吃${food}`)
}

// 子类
function Cat (name) {
  // 通过构造函数继承实例的属性和函数
  Animal.call(this)
  this.name = name
}
// 通过原型继承原型对象上的属性和函数
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat

let cat = new Cat('Tom')
console.log(cat.name) // Tom
cat.sleep() // Tom正在睡觉
cat.eat('猫罐头') // Tom正在吃猫罐头
```

{% title h3, 组合继承的优点 , warning %}

{% title h4, 既能继承父类实例的属性和函数，又能继承原型对象上的属性和函数 , red %}

{% title h4, 既是子类的实例，又是父类的实例 , red %}

{% title h4, 不存在引用属性共享的问题 , red %}
构造函数作用域优先级比原型链优先级高，所以不会出现引用属性共享的问题。

{% title h4, 可以向父类的构造函数中传参 , red %}

{% title h3, 组合继承的缺点 , warning %}

{% title h4, 父类的实例属性会被绑定两次 , red %}
在子类的构造函数中，通过`call()`函数调用了一次父类的构造函数；在改写子类的`prototype`属性，生成的实例时又调用了一次父类的构造函数。

{% title h2, 寄生组合继承 %}
组合继承方案已经足够好，但是针对其存在的缺点，我们仍然可以进行优化。

**在进行子类的`prototype`属性的设置时，可以去掉父类实例的属性的函数**。

```js
  //父类
function Animal (age) {
  // 实例属性
  this.name = 'Animal'
  this.age = age
  this.skill = ['eat', 'jump', 'sleep']
  // 实例函数
  this.sleep = function () {
    console.log(this.name + '正在睡觉')
  }
}
// 原型函数
Animal.prototype.eat = function (food) {
  console.log(`${this.name}正在吃${food}`)
}
// 子类
function Cat (name) {
  // 继承父类的实例和属性
  Animal.call(this)
  this.name = name
}
// 继承父类原型上的实例和属性
Cat.prototype = Object.create(Animal.prototype)
Cat.prototype.constructor = Cat
let cat = new Cat('Tom')

console.log(cat.name) // Tom
cat.sleep() // Tom正在睡觉
cat.eat('猫罐头') // Tom正在吃猫罐头
```
>其中最关键的语句：
>
>Cat.prototype = Object.create(Animal.prototype)
>
>只取父类Animal的prototype属性，过滤掉Animal的实例属性，从而避免了父类的实例属性绑定两次。

这种寄生组合式继承方式，基本可以解决前几种继承方式的缺点，较好地实现了继承想要的结果，同时也减少了构造次数，减少了性能的开销。

**整体看下来，这六种继承方式中，寄生组合式继承是这里面最优的继承方式。**
{% title h2, 总结 %}

![继承](/medias/extends.png)