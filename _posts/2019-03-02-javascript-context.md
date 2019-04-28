---
layout: post
title: JS中的执行上下文和执行栈
author: 炽凌
date: 2019-03-01
categories: JavaScript
tags: Javascript
comments: true
finished: true
---
__执行上下文的类型：__

- 全局执行上下文

  全局执行上下文指向全局对象，只有一个，在浏览器中的执行对象就是window

- 函数执行上下文

- EVAL执行上下文

__执行栈/调用栈__

用于存储在代码执行期间创建的所有执行上下文，执行栈是一个LIFO的结构

__执行上下文的创建__

执行上下文的分两个阶段创建

- 创建阶段
- 执行阶段

创建阶段所做的工作：

1. 确定this的值，也被称为this binding
2. 词法环境组件创建
3. 变量环境组件被创建

伪代码：

```
ExecutionContext = {
    ThisBinding = <this value>,
    LexicalEnvrironment = {...},  // 词法环境
    variableEnvironment = {...}  // 变量环境
}
```

This Binding

- 全局执行上下文中，this的值指向全局对象，浏览器中的全局对象就是window，也就是全局环境中的this指向window，nodeJS中指向这个文件的module对象
- 函数执行上下文中，this的值取决于函数的调用方式，具体的绑定规则有__默认绑定__、__隐式绑定__、__显示绑定__、 __new绑定__、__箭头函数的this指向规则__等

词法环境的组成：

- 环境记录   存储变量和函数声明的实际位置
- 对外部环境的引用   可以访问其外部词法环境

词法环境的类型：

- 全局环境
- 函数环境

变量环境：变量环境也是一个词法环境，具有上面定义的词法环境的所有属性，ES6中，词法环境和变量环境的区别就在于前者用于存储函数声明和变量（let和const绑定），而后者仅用于存储变量(var)绑定