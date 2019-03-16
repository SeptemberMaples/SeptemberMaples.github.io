---
title: ES6——块级作用域绑定
date: 2019-03-17 01:19:26
tags: 
  - es6
  - js
  - 学习笔记
categories:
  - 前端
---

我们知道, javascript在ES6之前的变量声明机制一直令我们感到困惑，何时创建变量要看怎么声明变量。ES6的新语法可以帮助我们更好地控制作用域。

## var声明及变量提升（Hoisting）机制

在函数作用域或全局作用域中通过关键字 *var* 声明的变量，无论实际上是在哪里声明，都会被看到是在当前作用域顶部声明的变量，也就是我们常说的 *提升机制* 。如下面函数：
<!-- more -->
```js
  function getValue(condition) {
    if(condition){
      var value = 'hello world';
      // 其他代码

      return value;
    } else {
      // 此处可访问变量value, 值为 undefined

      return null;
    }

    // 此处可访问变量value, 值为 undefined 
  }
```
实事上并不是当conditon的值为true时才会创建变量value，无论如何都会被创建。在预编译阶段，javascript引擎会将上面的 getValue函数修改成下面这样：
```js
  function getValue(conditon) {
    var value;
    if(condition) {
      value = 'hello world';

      // 其他代码

      return value;
    } else {
      return null;
    }
  }
```
变量value的声明被提升到函数顶部，而初始化操作依然在原处执行。也意味着在else子句中也可以访问该变量，由于此时该变量尚未初始化，所以值为 undefined。

因为javascript的变量提升机制可能会导致一些bug，所以ES6引入块级作用域来强化对变量生命周期的控制。

## 块级声明
块级声明用于声明在指定块的作用域外无法访问的变量。块级作用域（也称词法作用域）存在于：
- 函数内部
- 块中（{}中间的区域）

### let声明
ES6用let代替var声明变量，就可以把变量作用域限制在当前代码块中。let声明的变量也不会提升到作用域顶部，所以通常将let声明放在封闭代码块的顶部。
```js
  function getValue(conditon) {
    if(condition) {
      let value = 'hello world';

      // 其他代码

      return value;
    } else {
      // 变量value此处不存在

      return null;
    }

    // 变量value此处不存在
  }
```

### const声明
ES6用const关键字声明常量。const声明常量时必须进行初始化，且一旦设定不可更改。
```js
  // 有效常量
  const max = 30;

  // 语法错误： 常量未初始化  
  // Uncaught SyntaxError: Missing initializer in const declaration
  const min;
```
#### const声明对象
const不允许修改绑定，但允许修改值。也就是说const初始化声明对象后，可以修改对象的属性值。
```js
  const person = {
    name: 'Simon'
  }
  // 可以修改属性name的值
  person.name = 'Young'

  // 语法错误
  person = {
    name: 'Jack'
  }
```

### let 和 const
const 和 let都是块级标识，只在当前代码块内有效，一旦执行到块外会立即销毁；也不会提升至作用域顶部。

此外，作用域中已存在的标识符，不可再次用let或const声明。
```js
  var count = 30;

  // 语法错误
  let count = 30;
```

```js
  var msg = 'hello';
  let name = 'Simon';

  // 语法错误
  const msg = 'world';
  const name = 'Jack';
```

## 循环中的块作用域绑定
js最常见的for循环：
```js
  for (var i = 0; i < 10; i++) {
    process(items[i]);
  }

  // 这里仍可访问变量i
  console.log(i); // 10
```
如上, var声明得到了提升，变量i在循环结束后仍可访问。如果用let声明变量就能解决上面的问题。
```js
  for (let i = 0; i < 10; i++) {
    process(items[i]);
  }

  // 变量i在此不可访问，报错。
  console.log(i); 
```
i变量只存在于for循环中，一旦循环结束就被销毁，在其他地方无法访问。

## 循环中的函数
长久以来，var声明让开发者在循环中创建函数变得很困难，因为变量在循环之外还能访问。
```js
  var funcs = [];

  for (var i = 0; i < 10; i++ ) {
    funcs.push(function(){
      console.log(i)
    })
  }

  funcs.forEach(function(func) {
    func(); // 输出10次数字 10
  })
```
我们期望的结果是输出0~9，但却输出了10次数字10。这是因为循环里的每次迭代同时共享着变量i,循环内部创建的函数全都保留了对相同变量的引用。循环结束时变量i的值为10，所以每次调用`console.log(i)`都会输出数字10。

为解决这个问题，之前通常会在循环中使用*`立即调用函数表达式（IIFE）`*，强制生成计数器变量：
```js
  var funcs = [];

  for (var i = 0; i < 10; i++ ) {
    funcs.push((function(value){
      return function() {
        console.log(value)
      }
    }(i)));
  }

  funcs.forEach(function(func) {
    func(); // 输出10次数字 10
  })
```
在循环内部，IIFE表达式为接受的每一个变量i创建了一个副本并存储为变量value。这个变量的值就是相应迭代创建的函数所使用的值，所以调用每个函数都会像从0到9循环一样得到期望的值。

通过上文的let和const的学习，可以使用块级绑定轻松实现上面的需求。

## 循环中的let声明
```js
  var funcs = [];

  for (let i = 0; i < 10; i++ ) {
    funcs.push(function(){
      console.log(i)
    })
  }

  funcs.forEach(function(func) {
    func(); // 输出数字 0~9
  })
```
每次循环的时候 let 声明都会重新创建一个变量 i ,并将其初始化为 i 的当前值，所以循环内部创建的每个函数都能得到属于它们自己的 i 的副本。  对于 `for-in`循环和`for-of`循环来说也一样。
```js
  var funcs = [],
      obj = {
        a: true,
        b: true,
        c: false
      }
  for(let key in obj) {
    funcs.push(function() {
      console.log(i)
    })
  }

  funcs.forEach(function(func) {
    func(); // a, b, c
  })
```
*let 声明在循环内部的行为是标准中专门定义的，它不一定与 let 的不提升特性有关。*

## 循环中的const声明
对于普通的for循环来说可以在初始化变量时使用const，但要更改这个变量就会报错：
```js
  var funcs = []

  // 完成一次迭代后报错
  for (const i = 0; i < 10; i++) {
    funcs.push(function() {
      console.log(i)
    })
  }
```
如上，变量 i 被声明为常量。在第一次迭代中i是0，迭代成功。然后执行i++，试图修改常量i，因此抛出错误。 所以，如果后续循环不会修改此变量，可以使用const声明。
在 `for-in`循环和`for-of`循环中使用 const 时的行为和使用 let 一致。

## 全局块作用域绑定

let、const和var的另一个区别是它们在全局作用域中的行为。当var在全局作用域中声明一个变量时，会创建一个全局变量为全局对象（浏览器环境中的window对象）的属性。如：
```js
  var demo = 'Hello, world.'
  console.log(window.demo); // "Hello, world."
```
在全局作用域中使用 let 和 const ，会在全局作用域下创建一个新的绑定，但该绑定不会添加为全局对象的属性。
```js
  let author = 'Simon';

  console.log(author); // 'Simon'
  console.log(window.author === author); // false

  const age = 30;
  console.log(age); // 30
  console.log('age' in window); // false
```

## 块级绑定最佳实践
js开发者迁移到ECMAScript6后，`默认使用const`，只有确实需要改变变量的值时使用let。因为大部分变量初始化后应该再改变，而预料外的变量值的改变是很多bug的源头。