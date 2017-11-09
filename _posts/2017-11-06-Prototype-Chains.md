---
layout: post
tags: [js] # add tag
img: js-head.png
---

# Prototype Chains (原型链)

> 原型链是创建于其他对象类似的一种机制，使用委托的方式使一个对象具有另一个对象的属性和方法

## 举例

```js
var gold = {a:1};
console.log(gold.a);

var rose = Object.create(gold);
rose.b = 2;
// 可以找到委托对象的属性
console.log(rose.a);

gold.z = 3;
// 当委托对象变化的时候，依然可以找到
console.log(rose.z);

<!--
结果是：
1
1
3
-->
```

[Object.prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)

## 许多对象(如数组等)的实现就是通过委托

> 所有 javascript 对象都有一个最顶级的委托对象，里面存放基本的方法和属性。这个最顶级的对象（委托原型）又委托一些对象，用于存放不同类型的属性和方法的定义（如数组原型，Map 原型）。

<img src="{{site.baseurl}}/assets/img/15099575363016.jpg" alt="Prototype-Chains"/>





