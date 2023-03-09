---
title: Advanced JavaScript
date: 2022-01-23 01:19:00
tags: javascript
swiper: false
swiperDesc: 'Advanced JavaScript
'
categories: 前端
---

# Advanced JavaScript

## JS Engine

Javascript is an interpreted language.

What is this engine that we speak of well ?

If I write some code like let's say cost isHappy equals to true Well we just wrote some javascript

Now how do we read this or how does the computer read this ?

```
const isHappy = true
```

JS => engine => computer

##### Who created the first Javsscript Engine ?

Brendan Eich. => SpiderMonkey which is what Firefox still

### inside of V8 Engine

![image-20220102213441259](https://s3.bmp.ovh/imgs/2022/01/97d547a589bf66bb.png)

It looks something like this we give it a js file and first is does something called lexical analysis which breaks the code into something called tokens to identify their meaning. So that we into know what the code is trying to do and these tokens are formed into what wo call an AST that is an abstract syntax tree. So we pass the code that is we try and figure out how the text is divided up based on Keywords from js and it gets formed into this tree like structure called abstract syntax tree. In once in this form it goes through something called an interpreter profiler compiler and we get some. Code that are CPU you on our computers is going to understand to give it instructions and you can think of this whole process again which we're going to talk about in more detail coming up as something like this.

```
function jsengine(code) {
	return code.split(/\s+/)
}
code('const a = 6') //
```

##### What problem do u see with everybody creating their own engines in js ?

V8 is an ECMAScript engine

### interpreters and compilers

In programming there are generally two ways of translating to machine language or something that our computers can understand and we're going to talk about here actually applies to most programming languages not just JS Python Java C++ any language u can think of is going to use some of these concepts. So it's very important let's start with the first one the interpreter. With an interpreter, what we do is we translate and read the files line by line on the fly. Let's have a look at how this work.

```
function sum(a, b) {
	return a + b
}
for (let i = 0; i < 1000; i++) {
	sum(5, 4)
}
```

If we give this file to an interpreter the translation happens line by line on the fly and this is how u think the code should be run right? Because interpreting code simply means taking a set of instructions like we have over here and returning an answer and doing something with that code.

But what about a compiler?

A compiler I like an interpreter doesn't translate on the fly. What it does is it works ahead of time to create a translation of what code we've just written and it compiles down to usually a language that can be understood by our machines.

You see the definition itself is actually a little bit fuzzy in some respects all languages have to be interpreted and compiled because it has to run it has to be interpreted and it also has to most likely get translated into something low level like machine code for example.

```
// JS
let result = 1 + obj.x

// V8 bytecode
LdaSmi [1]
Star r0
LdaNamedProperty a0, [0], [4] Add r0, [6]

// Machine code ...
```

First, **interpreters are quick to get up and running right** because if we go to our example with an interpreter We don't have to convert this into an another language like language X like I showed.

But there's a con with using an interpreter and this is same problem that Google had back in to day when they had Google Maps running. a lot of js but it'll get slower and slower and slower because the problem with **interpreter is that when you're running the same code more than once. For example if you're in a loop like this one over here where we're running this piece of code over and over even though it gives us the same result it can get really really slow** the complier actually helps us here. It takes a little bit more time to start up because it has to go through that compilation step at the beginning go through our code understand it and spit it out into a another language but the complier will be smart enough that when it sees code like this and this is obviously a simplified version of it but if he sees code like this that we just loop over and it has the same inputs returning the same outputs. Well it can actually just simplified this code and instead of calling this function multiple timers can just replace this function with something like nine because we know that we want to return nine because **a compiler doesn't need to repeat the translation for each pass through in that loop the code generated from it is actually faster and these sort of edits that compliers do are called optimization**

so let's go back to that question interpreter or complier which one is better.

**They both have their pros and cons complier obviously takes a little bit longer to get up and running but the code is going to eventually run faster, interpreter that is really fast to get up and running but unfortunately doesn't do any optimizations.**

##### Is there a way that we can get the best of both worlds and this is what some engineers came up with in late 2000 and if we use Google as our example with the V8 engine what if we combine the best of both ?

**JIT compiler or just in timer Compiler** and this is exactly what browser started doing browsers started mixing compliers specifically these JIT compilers for just in time compiler to make the engines faster. So let's see how V8 engine does this.

This old man that's checking out our code in this profiler also called a monitor, monitors and watches our code as it runs and makes notes on how we can optimize this code sch as how many times it is being run. What types are used and how we can possibly optimize this and using this profiler as the code is running through our interpreter which tells our browser what to do if the same lines of code are run a few times. We actually pass off some of this code to the compiler or a JIT compiler adjust in time compiler because as the code is running, the interpreter is going to say here's some code for u to optimize passes it off to the compiler and the compiler as the application is running takes a code and compiled it or modifies it.

**the compiler is imperfect it can make mistakes and it can try to optimize code that actually does the opposite. And if it makes a mistake and it does something unexpected it does something called D optimization which takes even longer time to reverted back to the interpreter.**

### compare with other languages

.exe mostly be created by C++.

##### Is Js an interpreted language ?

not technically, technically it depends on the implementation.

In order to help the js engine we want to be really really careful with these on the left:

- eval()
- arguments
- for in (keys instance of)
- with
- delete
- inline-caching
- hidden classes

```
    // inline-caching
    function findUser(user) {
      return `found ${user.firstName} ${user.lastName}`
    }
    const userData = {
      firstName: 'W',
      lastName: 'Q'
    }
    console.log(findUser(userData));

    // hidden classes
    function Animal(x, y) {
      // should add properties a and b here
      this.x = x;
      this.y = y
    }
    const obj1 = new Animal('aa', 'bb')
    const obj2 = new Animal('cc', 'dd')
    obj1.a = 30;
    obj1.b = 100
    obj2.a = 100
    obj2.b = 30

    delete obj1.x // if I delete the property here of the object well again I change the hitting classes so that they don't match up anymore
```

##### Why not just use machine code from the beginning ?

You see if javascript were compile then either compilation would have to be super fast because remember. our js files get send from the server to the browser. So the compiling has to happen on the browser or the competing browsers that is Firefox.

## WebAssembly

We have the standard binary executable format called WebAssembly and this is what we didn't have in nineteen ninety five we didn't have the competing browsers agreeing on this format where we can compiler code all the way down to WebAssembly. This exectable format so that it runs really fast on the browser instead of having to go through that entire js engine proess.

## Call Stack and Memory Heap

**Call Stack => first in last out**

(anonymous) => global anonymous function

### garbage collection

js automatically frees up this memory that we no longer use and will collect out garbage.

Mark and sweep

**keep in mind that the execution of the loop also aided in the crash**

**memory leaks are pieces of memory that the application have used in the past but it's not needed any longer but has not yet been returned back to us to the poor free memory.**

```
global variale
var a = 1
<script>var z = 1</script>
<script>var zz = 2</script>
<script>var z = 4</script>

event listener
var element = deocument.getElementById('button')
element.addEventListener('click', onclick)

setinterval
setInterval(() => {
	// referencing objects...
})
```

## JS Runtime

when Call Stack meet Web API(like setimeout, fetch) , I'm going to just send it off to the web API so this gets moved away from the call Stack and gets send to the Web API.

![image-20220103105252517](https://s3.bmp.ovh/imgs/2022/01/6183205935f89b97.png)

## Execution Context

![image-20220103141108251](https://s3.bmp.ovh/imgs/2022/01/b010bdc9a5d6093d.png)

this => window(Global Object)

## Hoisting

```
    var favouriteFood = 'grapes';

    var foodThouts = function () {
      console.log("Original favourite food " + favouriteFood);
      var favouriteFood = 'sushi'
      console.log("New favourite food " + favouriteFood);
    }

    foodThouts()
    // Original favourite food undefined
	   New favourite food sushi
```

```
'use strict'
	function weird() {
      dd = 'xx'
      return dd
    }
```

## Block scope

```
if (2 > 1) {
	var secret = '321'
}
if (2 > 1) {
	let secret = '123'
}
```

```
    function loop() {
      for (var i = 0; i < 5; i++) {
        console.log(i);
      }
      console.log('final', i)
    }

    loop() // 0 1 2 3 4 final 5

    function loop() {
      for (let i = 0; i < 5; i++) {
        console.log(i);
      }
      console.log('final', i)
    }

    loop() // 0 1 2 3 4 error
```

## this Keyword

**`this` is the object that the function is a property of.**

```
function a() {
	console.log(this)
}
a() // window <=> window.a()
function a() {
	'use strict'
	console.log(this)
} // undefined
```

```
    // 1. gives methods acess to their object
    // 2. execute same code for multiple object
    const obj = {
      name: 'Billy',
      sing() {
        return 'lalala' + this.name
      },
      singAgain() {
        return this.sing() + '!'
      }
    }
    console.log(obj.singAgain());

    function importantPerson() {
      console.log(this.name + '!'); // change once
    }
    var name = 'Sunny'
    const obj1 = {
      name: 'Cassy',
      importantPerson
    }
    const obj2 = {
      name: 'Jacob',
      importantPerson
    }
    obj1.importantPerson()
```

```
    const a = function () {
      console.log('a', this);
      const b = function () {
        console.log('b', this);
        const c = {
          hi() {
            console.log('c', this);
          }
        }
        c.hi()
      }
      b()
    }
    a() // window.a(b(c.hi()))  b() => window
```

```
    const obj = {
      name: 'Billy',
      sing() {
        console.log(this);
        const anotherFunc = () => {
          console.log(this);
        }

        // const anotherFunc = function(){} // window
        return anotherFunc
      }
    }

    obj.sing()() // obj obj
```

## call, apply, bind

```
    const wizard = {
      name: 'Merlin',
      health: 50,
      heal() {
        return this.health = 100
      },
    }

    const archer = {
      name: 'Robin',
      health: 30
    }

    wizard.heal.call(archer)
```

```
    function multiply(a, b) {
      return a * b
    }
    let multiplyByTwo = multiply.bind(this, 2)
    let multiplyByTen = multiply.bind(this, 10)

    console.log(multiplyByTwo(4)); // 8
    console.log(multiplyByTen(4)); // 40
```

## Type Coercion

```
Object.is(+0, -0) // false
```

## First class Citizens

```
var stuff = function() {}

function a(fn) {
	fn()
}

function b() {
	return function c() {}
}
```

## HOF

Higher order function are simply a function that can take a function as an argument or a function that returns a function.

## Closure

We have these things called closures in Js because of two things that we get. One is the fact that in Js function are a first citizen. We can pass functions around like data like any other type in Js. We alse have this idea of lexical scope that is the Js engine knows based on where our code is written we even run the code. Each function has access to and closure is simply that a combination of function and the lexical environment from which it was declared. Closures allow a function to access variables from an enclosing scope or environment even after it leaves the scope in which it was declared.

```
function a() {
  let grandpa = 'grandpa'
  return function b() {
    let father = 'father'
    return function c() {
      let son = 'son'
      return `${grandpa} ${father} ${son}`
    }
  }
}
a()()()
```

**Js engine is going to keep any thing that's still being referenced**

```
function callMe() {
  setTimeout(function() {
    console.log(call)
  }, 2000)
  const call = "Hi!"
} // will run 'Hi'
```

```
// Memory efficient
function heavyDuty(index) {
  const bigArray = new Array(7000).fill('🤣')
  console.log('created') // run 4times
  return bigArray[index]
}

heavyDuty(688)
heavyDuty(688)
heavyDuty(688)
heavyDuty(688)

function heavyDuty2() {
  const bigArray = new Array(7000).fill('🤣')
  console.log('created Again') // run one time
  return function(index) {
    return bigArray[index]
  }
}
const getHeavyDuty = heavyDuty2()
getHeavyDuty(688)
getHeavyDuty(788)
getHeavyDuty(888)
```

```
// Encapsulation
const makeNuclearButton = () => {
  let timeWithoutDestruction = 0
  const passTime = () => timeWithoutDestruction++
  const totalPeaceTime = () => timeWithoutDestruction
  const launch = () => {
    timeWithoutDestruction = -1
    return '💥'
  }
  setInterval(passTime, 1000)
  return {
    // launch, hide for safe
    totalPeaceTime
  }
}

const ohno = makeNuclearButton()
ohno.totalPeaceTime()
// hiding of information that is unnecessary to be seen by the outside world or manipulated
```

## Prototype

```
let dragon = {
  name: 'Tanya',
  fire: true,
  fight() {
    return 5
  },
  sing() {
    if (this.fire) {
      return `i am ${this.name}`
    }

  }
}

let lizard = {
  name: 'Kiki',
  fight() {
    return 1
  },
}

const singLizard = dragon.sing.bind(lizard)
singLizard() // nothing

lizard.__proto__ = dragon
lizard.fire // true
dragon.isPrototypeOf(lizard) // true
```

```
let human = {
  mortal:true,

}
let socrates = Object.create(humnan) // replace __proto__
```

```
typeof Function
'function'
typeof Array
'function'
typeof Object
'function' // Object.constructor
```
