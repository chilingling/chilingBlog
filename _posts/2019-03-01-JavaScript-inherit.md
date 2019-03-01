---
layout: post
title: JavaScript的继承——《JavaScript高级程序设计读书笔记》
author: 炽凌
date: 2019-03-01
categories: JavaScript
tags: Javascript
finished: true
---
#### 构造函数、原型、和实例的关系

- 每个构造函数都有一个原型对象
- 原型对象都包含一个指向构造函数的指针
- 实例都包含指向原型对象的指针

#### 原型链继承

__基本思想__：利用原型让要给引用类型继承另一个引用类型的属性和方法

__本质__：重写原型对象，代之以一个新类型的实例

__注意点__：

- 继承后，当前实例的 constructor 指向的是父类，原因：当前实例的原型指向了父类，而父类的原型的constructor指向的是父类。
- 子类需要覆盖父类的方法时（声明一个与父类方法同名的方法），确保添加方法的代码放在替换原型的语句之后。
- 不能使用字面量创建原型方法

__存在的不足点__：

- 所有实例都共享父级的属性
- 不能向超类型的构造函数中传递参数，（没有办法再不影响所有对象实例的情况下，给超类型的构造函数传递参数。）

原型继承示例：

```javascript
function SuperType () {
    this.property = true; // 父级的属性
}
SuperType.prototype.getSuperValue = function () { // 父级的方法
    return this.property;
}
function SubType () {
    this.subproperty = false; // 子级的属性
}
// 继承SuperType
SubType.prototype = new SuperType();
// 添加子级的新方法
SubType.prototype.getSubValue = function () {
    return this.subpropertype;
}
// 重写超类型的方法，重写超类型的方法要放在替换原型的语句之后
SubType.prototype.getSuperValue = function () {
    return false;
}
//  实例化一个SubType
var instance = new SubType();
alert(instance.getSuperValue()) // true
// 测试实例与原型链中出现过的构造函数
console.log(instance instanceof Object) // true
console.log(instance instanceof SuperType) // true
console.log(instance instanceof SubType) // true
// instance 是Object、SuperType、SubType中任何一个类型的实例
console.log(Object.prototype.isPrototypeOf(instance)) // true
console.log(SuperType.prototype.isPrototypeOf(instance)) // true
console.log(SubType.prototype.isPrototypeOf(instance)) // true
```

#### 借用构造函数/伪造对象/经典继承

__基本思想/原理__： 在类型构造的内部函数调用超类型的构造函数

__语句摘录__ ：函数只不过时在特定环境中执行代码的对象，因此通过apply()和call()方法也可以在（将来新创建的对象上执行构造函数）

好处：每个对象上都有自己的超类型的属性的副本，我们也可以在调用超类型的构造函数的时候传递参数呢

__存在问题：__ 

- 每个对象上都有超类型的属性的副本的同时还有超类型的方法的副本，也就是无法实现函数复用。
- 超类型的原型中定义的方法，对子类型也不可见

示例代码：

```javascript
function SuperType () {
    this.colors = ["red", "blue", "green"];
}
function SubType () {
    // 在这个构造函数里面继承SuperType
    SuperType.call(this);
    // 还可以传递参数，比如
    //SuperType.call(this, argument1, argument2)
}
// 创建SubType的实例
var instancel = new SubType();
instancel.colors.push("black");
console.log(instancel.colors); // "red, blue, green, black"
var instancel2 = new SubType();
console.log(instancel2.colors); // "red,blue,green"
```



#### 组合继承

__基本思想/实现原理：__ 

- 将原型链和借用构造函数的技术组合到一块，从而发挥两者之长的一种继承模式
- 使用原型链实现对原型属性和方法的继承
- 通过构造函数来实现对实例属性的继承

示例代码：

```javascript
function SuperType (name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function () {
    console.log(this.name);
}
function SubType (name, age) {
    // 继承属性
    SuperType.call(this, name);
    自己的实例属性
    this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();
// 将构造函数重新指向自己
SubType.prototype.constructor = SubType;
// 自己的方法
SubType.prototype.sayAge = function () {
    console.log(this.age)
}
var instancel = new SubType("chiling", 21);
instancel.colors.push("black");
console.log(instancel.colors); // "red,blue,green,black"
instancel.sayName();  // "chiling"
instancel.sayAge(); // 21

var instancel2 = new SubType("chiling1", 22);
console.log(instancel2.colors); // "red,blue,green"
instancel.sayName() // chiling1
instancel.sayAge() // 22
```

 额外的好处：instanceOf 和isPrototypeOf 也能够用于识别基于组合继承创建的对象

#### 原型式继承

__实现思想：__ 基于已有的对象创建新对象，同时还不必因此创建自定义类型

比如这个object函数：

```javascript
function object (o) {
    function F(){};  // 临时性的构造函数
    F.prototype =  0; // 将传入的对象作为这个构造函数的原型
    return new F(); // 返回这个临时类型的新实例
}
```

__从本质上来讲，object()对传入其中的对象执行了一次浅复制__ 

一个例子：

```javascript
var person = {
    name: "chiling",
    friends: ["shellby", "Court", "Van"]
}
var anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

console.log(person.friends);   // "Shellby,Court,Van,Rob,Barbie"
```

从上面的例子我们可以看到，anotherPerson和yetAnotherPerson会共享继承的person的属性，一方修改，另一方也将得到响应

ES5中实现这个继承模式的方法叫做__Object.create()__ ，该方法接收两个参数，一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。__传入一个参数的情况下，Object.create()与Object()方法的行为相同__

注意：包含引用类型值得属性始终会共享相应的值。就像使用原型模式一样

#### 寄生式继承

__实现思想/原理：__ 创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象。

代码示例：

```
function createAnother (original) {
	var clone = object(original);
	clone.sayHi = function () {
        console.log("sayHi);
	}
	return clone;
}
```

存在的问题：不能做到函数复用，降低效率

#### 寄生组合式继承(最理想的继承范式)

__实现思想/原理：__ 组合继承和寄生式继承的结合，结合了两者的优点。变得更强大。

__寄生组合式继承就是通过借用构造函数来继承属性，通过原型链的混成方式来继承方法__

背后的思路：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型的原型的一个副本

基本模式代码

```
function inheritPrototype (subType, superType) {
    var prototype = object(superType.prototype) // 创建对象
    prototype.constructor = subType;
    subType.prototype = prototype;
}
```



