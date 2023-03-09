---
title: javascript 求数组的最大值和最小值 (6种方法)
date: 2021-05-29 18:24:02
tags: javascript
swiper: true
swiperDesc: '通过prototype属性扩展min()函数和max()函数</br>
借助Math对象的min()函数和max()函数</br>
算法2的优化</br>
借助Array类型的reduce()函数</br>
借助Array类型的sort()函数</br>
借助ES6的扩展运算符
'
categories: 前端
---
{% title h2, 通过prototype属性扩展min()函数和max()函数 %}

算法1的思路是在自定义min()和max()函数中，通过循环由第一个值依次与后面的值作比较，动态更新最大值和最小值，从而找到结果。


```javascript
        // 最小值
        Array.prototype.min = function () {
            let min = this[0];
            let len = this.length;
            for (let i = 1; i < len; i++) {
                if (this[i] < min) min = this[i]
            }
            return min
        }
        // 最大值
        Array.prototype.max = function () {
            let max = this[0];
            let len = this.length;
            for (let i = 1; i < len; i++) {
                if (this[i] > max) max = this[i]
            }
            return max
        }
        // 结果
        console.log(arr.min()); // 1
        console.log(arr.max()); // 9
```
{% title h2, 借助Math对象的min()函数和max()函数 %}
算法2的主要思路是通过apply()函数改变函数的执行体，将数组作为参数传递给apply()函数。这样数组就可以直接调用Math对象的min()函数和max()函数来获取返回值。

```javascript
        Array.min = function(array) {
            return Math.min.apply(Math, array)
        }
        // 最大值
        Array.max = function (array) {
            return Math.max.apply(Math, array)
        }
        // 结果
        console.log(Array.min(arr)); // 1
        console.log(Array.max(arr)); // 9
```
{% title h2, 算法2的优化 %}
在算法2中将min()函数和max()函数作为Array类型的静态函数，但不支持链式调用，我们可以利用对象字面量进行简化。

```javascript
        // 最小值
        Array.prototype.min = function() {
            return Math.min.apply({}, this)
        }
        // 最大值
        Array.prototype.max = function () {
            return Math.max.apply({}, this)
        }
        // 结果
        console.log(arr.min()); // 1
        console.log(arr.max()); // 9
```
与算法2不同的是，在验证时，因为min()函数和max()函数属于实例方法，所以可以直接通过数组调用。
上面的算法代码中apply()函数传入的第一个值为{},实际表示当前执行环境的全局对象。第二个参数this指向需要处理的数组。
由于apply函数的特殊性第一个参数，指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。所以我们也可以将第一个参数设置为null、undefind。
{% title h2, 借助Array类型的reduce()函数 %}
算法4的主要思想是reduce()函数不设置initialValue初始值，将数组的第一个元素直接作为回调函数的第一个参数，依次与后面的值进行比较。当需要找最大值时，每轮累加器返回当前比较中大的值；当需要找最小值时，每轮累加器返回当前比较中小的值。

```javascript
        // 最小值
        Array.prototype.min = function () {
            return this.reduce((pre, cur) => {
                return pre < cur ? pre : cur
            })
        }
        // 最大值
        Array.prototype.max = function () {
            return this.reduce((pre, cur) => {
                return pre > cur ? pre : cur
            })
        }
        // 结果
        console.log(arr.min()); // 1
        console.log(arr.max()); // 9
```
{% title h2, 借助Array类型的sort()函数 %}
算法5的主要思想时借助数组的原生sort()函数对数组进行排序，排序完成后首尾元素即是数组的最小、最大元素。
默认的sort()函数在排序时时按照字母顺序排序的，数字会被按照字符串处理。例如数字 18 会被当做"18"处理，数字 6 被当"6"来处理，在排序时是按照字符串的每一位进行比较的，因为"1"比"6"要小，所以"11"排序时要比"6"小。对于数值类型的数组来说，这显然不合理。所以我们需要进行自定义排序。

```javascript
        let sortArr = arr.sort((a, b) => a - b)
        // 最小值
        sortArr[0]
        // 最大值
        sortArr[sortArr.length - 1]
        // 结果
        console.log(sortArr[0]); // 1
        console.log(sortArr[sortArr.length - 1]); // 9
```
{% title h2, 借助ES6的扩展运算符 %}

```javascript
        // 最小值
        Math.min(...arr)
        // 最大值
        Math.max(...arr)
        // 结果
        console.log(Math.min(...arr)); // 1
        console.log(Math.max(...arr)); // 9
```