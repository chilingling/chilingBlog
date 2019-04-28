---
layout: post
title: "[1,2,3].map(parseInt) 为什么是[1,NaN,NaN]？"
author: 炽凌
date: 2019-03-08
categories: JavaScript
tags: Javascript
comments: true
finished: true
---
> 在一次的笔试题中遇到的，当时理所当然的就以为答案是[1,2,3]，后来发现自己错了。便记录一下
>
> 这题的主要考察点是基础知识，`map`函数和`parseInt`函数

__我们先来看看map函数：__

[在MDN上的详细讲解](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map)

数组中的map函数接受一个函数作为一个处理的函数，并且数组中的每一项都调用该函数进行处理，处理完毕之后再返回一个全新的数组

我们重点来看一下这个处理函数

```javascript
var new_array = arr.map(function callback(currentValue[, index[, array]]){}[, thisArg])
```

分析一下这个处理函数：处理的函数会接受三个参数，第一个是数组项本身，第二个是数组的index索引，第三个是被处理的数组本身。

__我们再来看看这个parseInt函数__

[MDN上的详细讲解](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)

parseInt函接受两个参数，第一个是被处理的string,第二个是转换的基数radix

其中，如果第一个参数不是string，会被调用ToString方法转换成string，并且字符串开头的空白符将会被忽略

第二个参数中，接收一个介于2和36之间的整数

__radix参数n存在时__，该函数就会把第一个参数string看成是n进制的表示，函数的返回值则是返回十进制的数字

比如：

```javascript
parseInt('123', 5) // 该函数会把'123'看成是5进制的表示，并且返回他的十进制的表示38
```

__radix参数不存在、参数为undefined、参数为0__等情况下，JavaScript作如下处理

- 如果参数字符串`string` 以`0X`或者`0X` 开头，则指定基础为16
- 如果参数字符串以0开头，则基础可能时8进制或者10进制
- 如果参数字符串以其他任何值开头，则基础是10（十进制）

------

看完了两个函数的要点，结合分析一下`[1,2,3].map(parseInt)`的运行过程是怎样的。

首先，map函数会对每个元素进行迭代调用parseInt函数，上面的代码就可以近似的等同为：

```javascript
[1,2,3].map(function (item, index) {
    return parseInt(item, index);
})
```

也就是说，每一次迭代传入parseInt函数的参数分别为：`1,0`,`2,1`,`3,2`，这个时候就看的很明白了，第一次的调用基数为零则进制为10，返回的仍是1，第二次的调用基数是1，但是字符串2大于1，超过了其表示范围，则返回NaN，第三次的调用和第二次类似

__如何解决？__

知道了问题的所在，对症下药也就比较容易了。比如：

```javascript
[1,2,3].map(function (item, index) {
    return parseInt(item, 10);
})
```

或者我们可以：

```javascript
[1,2,3].map(Number)
```

最后的反思，自己对这些函数都有一定的理解，并且在实际的编码过程中也有用到，但是就是理解的不够深刻，或者说在思考的时候并没有深入的思考他的运行过程。这个以后要有所加强