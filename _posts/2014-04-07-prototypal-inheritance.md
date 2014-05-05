---
layout: post
title: Javascript的继承
description: "探讨了集中可能的继承实现"
category: articles
tags: [nodejs, javascript, class, inheritance, prototype]
comments: true  
---
前戏：本文讨论的内容对 javascript 和 node.js 都适用，所以下面统称为 js 。

最近因为工作和学习的原因，接触了一些以前看过和没看过的资料，所以记下来，希望大家可以更好地理解 js 和原型 (prototype) 。

首先要牢记这几个点:

* js 是面向对象的编程语言
* js 是基于原型 (prototype) 的
* js 并没有实现传统的类 (class) 系统

最会引起困惑的应该是第二点。怎么理解呢？js中的每一个对象都有一个内置的属性叫 `__proto__` ，也就是这个对象的原型 (prototype) ，这个原型也是一个js的对象。如果我试图访问 `object.prop` ，而 `object` 本身又没有 `prop` 这个属性，js就会去 `object` 的原型对象 (`project.__proto__`) 上去找；如果这个原型对象里面依然没有 `prop` ，那么js就会去这个原型对象的原型对象 (`object.__proto__.__proto__`) 里面去找；直到这个原型对象的原型对象的...的原型对象是 null 为止。

其实类继承 (Class Based Inheritance) 和原型继承 (Prototype Based Inheritance) 其实都是为了满足 OOP 。简单一点来说，我们希望子类的对象可以继承父类定义的一些成员，增强代码的可重用性和抽象层级。

下面这段代码来自于 Douglas Crockford ，实现了最简单的原型继承:
{% highlight javascript %}
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

var obj = {a:1, b:2};
var newObj = object(obj);

newObj.a //=> 1
newObj.__proto__ === obj //=>true

//此时，newObj.a 并不是 newObj 自己的属性，而是顺着原型链找到的 obj 的属性
newObj.hasOwnProperty('a') //=>false
obj.hasOwnProperty('a') //=>true
newObj.a === obj.a //=>true

{% endhighlight %}
`object` 函数接受一个对象作为参数，生成一个继承该对象的新对象。当我们试图访问新对象的某个不存在的属性时，js会沿着原型链找到旧对象，然后在旧对象里面想办法找到该属性。
