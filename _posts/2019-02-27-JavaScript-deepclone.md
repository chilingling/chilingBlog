---
layout: post
title: JavaScript的深拷贝和浅拷贝
author: 炽凌
date: 2019-02-27
categories: JavaScript
tags: Javascript
comments: true
finished: true
---
# JavaScript的深拷贝和浅拷贝

> 拷贝，通常是指将某一个变量的值复制到另一个变量中。在我们的认知中，JavaScript的值类型主要有两种，一种是基本数据类型，一种是引用类型。我们常说的深拷贝和浅拷贝就是针对引用类型而言的。

__引用类型的拷贝__

复制一个对象a到b中，a跟b都指向同一个对象。修改b，a中的值也将被改变

例如：

```javascript
var a = {
    age: 2
}
var b = a
b.age = 3
console.info(a.age) // 3
```

这种拷贝的结果很多时候都不是我们想要的，我们希望拷贝了之后的对象b修改之后不会对a造成影响。

__浅拷贝__

浅拷贝的方法有三种：

- for in 遍历拷贝 
- Object.getOwnProperty() 获取属性拷贝
- Object.assign拷贝

先说说for in 拷贝，for in 循环会遍历对象和原型链上的属性，再利用Object.haOwnProperty(key)判断是否是该对象上的实例属性。如果是，我们就复制到目标对象上。

示例代码：

```javascript
var source = {
    age: 3
    a: {
        name: "chiling"
    }
}
var target = {}
for (var key in source) {
    if (source.hasOwnProperty(key)) {
        target[key] = source[key]
    }
}
console.log(source.age) // 3
console.log(target.age) // 3
target.age = 4
console.log(source.age) // 3
console.log(target.age) // 4
```



这样子，我们就可以将复制对象跟源目标对象隔离了，实现了我们想要的复制。但是，当被复制的属性还是一个引用类型的时候，还是会存在相同的问题，在上面的拷贝中，假如我们修改了target.a.name的属性，source对象中的source.a.name也将会被修改。

这就是浅拷贝，仅仅拷贝对象上第一层的实例属性，而深层次的拷贝仍然无法解决

其次是Object.getOwnProperty()，其中source为被拷贝对象，target为拷贝出来的新对象

```javascript
Object.getOwnPropertyNames(source).map(key => target[key] = source[key])
```

还有Object.assign()方法

```javascript
Object.assign(target, source)
```

下面说说深拷贝的方法：

第一种：简单粗暴的JSON序列化再还原的方法

```javascript
var target = JSON.parse(JSON.stringify(source))
```

实现原理：JSON的stringify将对象的属性和值序列化成字符串，再通过parse解析回对象，就完成了深层级的拷贝。

注意点：stringify不会将值为undefined或是null的属性序列化，所以，这一部分的值将无法得到复制

第二种：递归浅拷贝复制

```javascript
function deepClone (source) {
	// 不是对象
    if (Object.prototype.toString.call(source) !== '[object, object]') return source;
    var target = {}
    for (var key in source ) {
        if (source.hasOwnProperty(key)) { // 判断是否是实例上的属性
            if (Object.prototype.toString.call(source[key]) === '[object, object]') {  // 判断要复制的属性是否是对象，如果是，则递归调用复制函数
                target[key] = deepClone(source[key])
            } else if (Array.isArray(source[key])) { // 数组中的值也可能是对象，所以也得进行深层递归复制
                target[key] = source[key].map(item, deepClone(item))
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}
```

递归浅拷贝不难理解，但是如果要复制的对象层级非常深，就有可能造成栈溢出。

第三种：模拟二叉树遍历进行浅拷贝

```javascript
function deepClone (source) {
    var root = {};  // 新对象
    var stack = [   // 复制的栈，
        {
            parent: root,
            key: undefined,
            data: source
        }
    ]
    while (stack.length) {
        var node = stack.pop();
        var parent = node.parent;
        var key = node.key;
        var data = node.data;

        var res = parent;
        if (typeof key !== undefined)  {
            res = parent[key] = {};
        }
        for (var k in data) {
            if (data.hasOwnProperty(k)) {
                if (Object.prototype.toString.call(data[k]) === '[object, object]') {
                    stack.push({
                        parent: res,
                        key: k,
                        data: data[k]
                    })
                } else {
                    res[k] = data[k]
                }
            }
        }
    }
    return root;
}
```

这种方法适合于层级很深的对象的复制

__第四种：保持引用的复制__

```javascript
function deepClone04(source) {
    //===========
    var uniqueList = [];
    //===========
    var root = {};
    var stack = [
        {
            parent: root,
            key: undefined,
            data: source
        }
    ]
    while (stack.length) {
        var node = stack.pop();
        var parent = node.parent;
        var key = node.key;
        var data = node.data;

        var res = parent;
        if (typeof key !== undefined)  {
            res = parent[key] = {};
        }
        var uniqueData = find(uniqueList, data);
        // 数据已经存在
        if (uniqueData) {
            parent[key] = uniqueData.target;
            continue;
        }
        // 数据不存在
        // 保存数据，在拷贝数据种对应的引用
        uniqueList.push({
            source: data,
            target: res
        })
        for (var k in data) {
            if (data.hasOwnProperty(k)) {
                if (isObject(data[k])) {
                    stack.push({
                        parent: res,
                        key: k,
                        data: data[k]
                    })
                } else {
                    res[k] = data[k]
                }
            }
        }
    }
    return root;
}
function find(arr, item) {
    for (let i = 0; i<arr.length;i++) {
        if (arr[i].source === item) {
            return arr[i];
        }
    }
    return null;
}
```



这种方法适合于我们希望复制出来对实例属性对别的属性仍然能够保持一定的引用。

参考：

[颜海镜·深拷贝的终极探索](https://yanhaijing.com/javascript/2018/10/10/clone-deep/)

[jsmini/clone](https://github.com/jsmini/clone)