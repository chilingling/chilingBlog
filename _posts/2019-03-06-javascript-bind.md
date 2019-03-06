---
layout: post
title: 模拟实现ES5中原生bind函数
author: 炽凌
date: 2019-03-06
categories: JavaScript
tags: Javascript
finished: true
---
###### bind函数介绍

在JavaScript中，this的绑定是根据运行时的调用栈来动态绑定的（每个函数的this是在调用的时候被绑定的），this的指向取决于函数的调用位置，但是有时候我们希望某一个object中的this绑定到一个确定的对象中，我们可能就要用到bind函数来绑定

###### ES5中bind函数中的使用语法

`fun.bind(thisArg[, arg1[, arg2[,...]]])`

bind函数用例一：

```javascript
function foo (something) {
    console.log(this.a, something);
    return this.a + something;
}
var obj = {
    a: 2
}
var bar = foo.bind(obj);
var b = bar(3); // 2 3
console.log(b); // 5
```

了解了用法，那么我们就可以思考，怎么模拟实现呢？

#### 初级实现

```javascript
Function.prototype.bind = function (context) {
    var self = this;
   	var argsV = Array.prototype.slice.call(arguments, 1);
    return  function () {
        self.apply(context, argsV);
    }
}
```

###### 实现分析

参数context就是要绑定的对象（上下文context），然后函数体中我们第一行就保存了一个this引用，这个this，指向的是调用它（bind方法）的函数，比如用例一，this指向的就是foo对象，第二行，我们就将除context参数之外的参数额外保存起来了，这个称为预设参数。最后我们返回一个函数，这个函数里面使用apply方法显式绑定了参数context，从而得到我们想要的this指向

可是，仅仅这样的一个模拟实现其实还不够严谨，我们还要更严谨的一个实现。请看下面

#### 改进

1. 使用嗅探

```javascript
Function.prototype.bind = Function.prototype.bind || function (context) {
    //...初级实现
}
```

第一个改进就是一个嗅探方法，比如很多现代的浏览器都实现了这个方法，那么我们就不必要用自己的模拟实现了，一般都是用原生的速度更！

2. 颗粒化（curring）实现

```javascript
Function.prototype.bind = Function.prototype.bind || function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    return function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return self.apply(context, finalArgs);
    }
}
```

可能的用例：

```javascript
function list () {
    return Array.prototype.slice.call(arguments);
}
function addArguments(arg1, arg2) {
    return arg1 + arg2;
}
var list1 = list.bind(null, 37);
var list2 = list1(1, 2, 3) // [37, 1, 2, 3]
var result1 = addArguments.bind(null, 37);
var result2 = result1(5); // 37+5 = 42
```

3. 构造函数下的兼容

```javascript
Function.prototype.bind = Function.prototype.bind || function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    var F = function () {};
    F.prototype = this.prototype;
    var bound = function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        var finalArgs = args.concat(innerArgs);
        return self.apply(this instanceof F ? this : context || this, finalArgs);
    }
    bound.prototype = new F();
    return bound;
}
```

在你不知道的JavaScript第一卷中说的很清楚，this绑定规则中构造函数（new操作符）构造出来的函数（对象）指向的是调用new操作符的上下文，而不是之前绑定的this。上面一段代码就考虑了这种情况

首先，声明一个函数F,这个函数F继承了当前函数的原型，然后将返回的函数bound的原型指向函数F的一个实例,然后，在bound函数内部，我们判断当前的this的prototype是否指向F，如果是，则不硬绑定context，否则，则绑定context



4. 严谨的判断，判断当前的this是否是一个函数

```javascript
if (typeof this !== 'function') {
    throw new TypeError("Function.prototype.bind--- what is trying to be bind is not callable")
}
```



##### 叹为观止的ES5-shim实现

在[es5-shim源码](https://github.com/es-shims/es5-shim/blob/master/es5-shim.js)中，考虑了更多的兼容和模拟情况，有兴趣的可以读一读

#### 参考(其实很大一部分雷同，不知算不算抄袭)

[颜海镜的博客](https://zhuanlan.zhihu.com/p/25379434)

