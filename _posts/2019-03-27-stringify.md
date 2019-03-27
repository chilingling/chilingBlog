---
layout: post
title: JSON.stringify序列化的模拟实现
author: 炽凌
date: 2019-03-27
categories: JavaScript
tags: Javascript
finished: true
---
> 昨天面试的时候被问到如何实现，在电话面试中仅仅答出来了大概的思路，但是并没有完整去实现，后来找到一个MDN的polyfill，特此记录一下

在模拟实现这个方法之前，我们首先来看看他的转化语法，才能更好的模仿

> `JSON.stringify(value[,replacer[,space]])`
>
> 参数： value      比传的参数      将要序列化的值
>
> ​             replacer      可选参数   是一个数组或者函数   如果是一个函数   则每个序列化的值都会经过这个函数的处理，如果是数组，则只有在该数组中的键才会被序列化
>
> ​              space      缩进用的空格参数，用于美化输出， 参数是数字，则代表有几个空格（上限10， 小于一则没有空格），如果是字符串，则使用该字符串作为空格（最多前10个）

我们可以来看看几个基本例子：

#### 序列化基本类型：number、string、boolean、undefined、null、symbol

```javascript
JSON.stringify(1)  // "1"
JSON.stringify("1") // ""1""
JSON.stringify(true) // "true"
JSON.stringify(null) //  "null"
JSON.stringify(undefined) // undefined
JSON.stringify(Symbol) // undefined
```

对于这几个基本类型的转换，我们可以这样简单的实现：

```javascript
if (value === null) {
    return "null";
} else if (typeof value === 'number' || typeof value === 'boolean' || typeof value === 'string') {
    return value.toString();
} else { // 针对undefined和Symbol
    return void 0;
}
```

#### 序列化数组

>  数组的序列化过程中如果遇到了不可toString得值，该位置会被置为null

```javascript
JSON.stringify([1, "2", false, undefined, 5]) // "[1, "2", false, null, 5]"
```

对于数组，我们则可以这样来处理：

- 初始化的'['
- 遍历数组中的每一项，递归调用stringify，拼接上`', '`第一项则不用拼接
- 遍历完之后拼接上']'，返回

```javascript
var isArray = Array.isArray || function (a) { return Object.prototype.call(a) === '[object Array]' }
if (isArray(value)) {
    var ret = '['
    value.forEach(function (item, index) {
        ret += index === 0 ? '' : ', ' + stringify(item);
    })
    return ret + ']';
}
```

#### 序列化对象

首先是最常规情况下的对象

```json
JSON.stringify({a: 1}) // "{"a": "1"}"
```

- 使用for in 遍历他们的属性将他们的键和值都序列化并合成一个字符串（中间加一个`:`），全部存储到一个数组中
- 最后将他们使用`join`方法合成一个字符串，头尾分别加上`{` 和`}`,返回

```javascript
if (Object.prototype.toString.call(value) === '[object Object]') {
    var temp = [];
    for (var key in value) {
        if (value.hasOwnProperty(key)) {
            temp.push(stringify(key) + ':' + stringify(value[key]))
        }
    }
    return '{' + temp.join(', ') + '}';
}
```

#### 序列化日期对象

```javascript
if (Object.prototype.toString.call(value) === '[object Date]') {
    return '"' + value.toJSON === 'function' ? value.toJSON() : value.toString() + '"';
}
```

#### 序列化正则表达式

```javascript
if (OBject.prototype.toString.call(value) === '[object Regexp]') {
    return {}
}
```

#### 下面是MDN上的一个polyfill

```javascript
window.polyfillJSON = {
    parse: function (sJSON) { return eval('()' + sJSON + ')'); },
    stringify: (function () {
        // 转换成字符串
        var toString = Object.prototype.toString;
        // 判断是否是数组
        var isArray = Array.isArray || function (a) { return toString.call(a) === '[object Array]' };
        // 一些特殊的字符
        var escMap = { '"' : '\\"', '\\': '\\\\', '\b': '\\b', '\f': '\\f', '\n': '\\n', '\r': '\\r', '\t': '\\t' };
        var escFunc = function (m) { return escMap[m] || '\\u' + (m.charCodeAt(0) + 0x10000).toString(16).substr(1); };
        var escRE = /[\\"\u0000-\u001F\u2028\u2029]/g;
        return function stringify (value) {
            if (value == null) { // null
                return 'null'
            } else if (typeof value === 'number') { // 数字number
            	return isFinite(value) ? value.toString() : 'null';
            } else if (typeof value === 'boolean') { // 布尔值
                  return value.toString();
            } else if (typeof value === 'object') { // 函数、对象
                if (typeof value.toJSON === 'function') {
                    return stringify(value.toJSON());
                } else if (isArray(value)) {
                    var res = '[';
                    for (var i = 0; i < value.length; ++i) {
                        res += (i ? ', ': ':') + stringify(value[i])
                    }
                    return res + ']';
                } else if (toString.call(value) === '[object Object]') {
                    var temp = [];
                    for (var key in value) {
                        if (value.hasOwnProperty(key)) {
                            temp.push(stringify(key) + ':' + stringify(value[k]));
                        }
                    }
                    return '{' + temp.join(', ') + '}';
                }
            }
            // 日期对象，正则对象都在这里处理
            return '"' + value.toString().replace(escRE, escFunc) + '"';
        }
    })()
}
```

仅仅是如此吗？

其实还差的很远

- 如果replace传入了数组，数组的值代表将被序列化称为JSON字符串的属性名（针对对象）
- 如果传入了函数，这个函数可以接收两个参数：键和值，他们都将被序列化
- 使用传入的space作为替换空格数或者换行字符
- ....

最后，也就不出意外的被面试官挂掉了，哈哈哈，不过不应该失去信心，而是要从每一次的失败中寻找教训。最后才能有成长呢！

这里是[JSON2](<https://github.com/douglascrockford/JSON-js>)和[JSON3](<https://github.com/bestiejs/json3>)

参考：

- [MDN](<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON>)
- [MDN](<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify>)