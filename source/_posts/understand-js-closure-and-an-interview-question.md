---
title: 理解JS的闭包及关于闭包的经典面试题
date: 2019-04-23 10:59:16
tags: 
  - js
  - 面试题
categories: 
  - 前端
  - js
---

闭包是JS必须掌握的知识点，也几乎是面试必问题目。面试中应该会问到：**“什么是闭包？闭包的作用是什么？闭包有哪些使用场景？”**。了解一下吧。

### 什么是闭包
> 闭包是指有权访问另一个函数作用域中的变量的函数，创建闭包最常用的方式就是在一个函数内部创建另一个函数。

<!-- more -->

```js
function compare(key) {
  return function(obj1, obj2) {
    var val1 = obj1[key];
    var val2 = obj2[key];
    return val1 > val2 ? 1 : -1;
  }
}
```
以上是一个闭包的简单实现。 `compare` 函数中的内部函数（匿名函数）中 `var val1 = obj1[key];` 访问了外部函数的变量 `key`。即使这个内部函数被返回，并且在其他地方被调用了，但他仍然可以访问变量`key`。之所以能访问`key`,是因为内部函数的作用域链中包含 `compare` 函数的作用域。

### 闭包的作用有
- 模仿块级作用域(ES5中没有块级作用域)
- 封装私有变量
- 实现JS的模块

#### 模仿块级作用域
ES5中没有块级作用域的概念。这意味着在块语句中定义的变量，实际上是在包含函数中而非语句中创建的。如下
```js
function out(count){
  for(var i=0; i < count; i++){
    console.log(i);
  }
  console.log(i);  // 计数  5
}
out(5); // 返回  0，1，2，3，4，5
```
变量 `i` 定义在 out() 的活动对象中，因此从他定义开始，就可以在函数内部随处可以访问它。匿名函数可以用来模仿`块级作用域`避免这个问题。
用块级作用域（通常称为 **私有作用域**）的匿名函数语法：
```js
(function(){
  // 这里是块级作用域
})();
```
<u>以上代码*定义并立即调用了一个匿名函数*。将函数声明包含在一对圆括号中，表示它实际上是一个函数表达式。而紧随其后的另一对圆括号会立即调用这个函数。**匿名函数中定义的任何变量，都会在执行结束时被销毁。**</u>

上面代码等价于，先声明一个函数表达式，然后再调用。
```js
var someFn = function(){
  // 块级作用域
}
someFn();
```
> PS: 函数的声明方式有两种，一种是 函数声明，一种是 函数表达式:

```js
// 函数声明
function fn(){}
```
```js
// 函数表达式
var fn = function (){} // 将匿名函数赋值给一个变量
```
要将函数声明转化为函数表达式，只需要给它加上一对圆括号。
```js
(function(){
  // 这里是块级作用域
})();
```
无论什么地方，只要临时需要一些变量，就可以使用块级作用域，如改写上面的 out() 函数：
```js
function out(count){
  // 创建块级（私有）作用域
  (function() {
    for(var i=0; i < count; i++){
      console.log(i);
    }
  })();
  
  console.log(i);  // 报错。 i is not defined
}
out(5); 
```
匿名函数中定义的任何变量，都会在执行结束时被销毁。因此 `i` 只能在循环内部使用，使用后被销毁。而私有作用域中可以访问变量 `count`,是因为这个匿名函数是一个闭包，闭包可以访问作用域中的所有变量。

#### 封装私有变量
严格来讲, javascript没有私有成员的概念，所有对象属性都是公有的。但是有一个`私有变量`的概念。在任何函数中定义的变量，都被认为是私有变量，因为不能在函数外部被访问。私有变量包括函数的参数、局部变量和在函数内部定义的其他函数。
因为闭包可以通过作用域链访问包含函数的变量，所以利用这一点可以创建访问私有变量的公有方法。我们把**有权访问私有变量和私有函数的公有方法称为 <u>特权方法</u>**

有两种方式在对象上创建特权方法：

##### 在 构造函数 中定义特权方法：
```js
function MyObject() {
  // 私有变量和私有函数
  var privateVar = 10;
  function privateFn(){
    return false;
  }

  // 特权方法
  this.publishMethod = function() {
    privateVar++;
    return privateFn()
  }
}
```
创建MyObject的实例后，除了使用publishMethod()这一个特权方法外，没有其他办法可以直接访问 privateVar 和 privateFn()。

使用这种方式的不足是，构造函数对每个实例都会创建同样一组新的方法。  使用下面静态私有变量来实现特权方法可以避免这个问题。

##### 静态私有变量 
通过在私有作用域中定义私有变量或函数，同样可以创建特权方法：
```js
(function(){
  // 私有变量和私有函数
  var privateVar = 10;
  function privateFn(){
    return false;
  }

  // 构造函数
  MyObject = function(){};

  // 公有/特权方法
  MyObject.prototype.publishMethod = function(){
    privateVar++;
    return privateFn();
  }
})()
```
这个模式创建了一个私有作用域，并在内定义了私有变量和私有函数，然后定义了构造函数及其公有方法。公有方法是在原型上定义的。需要注意的是，`MyObject`构造函数使用的是函数表达式，并且没有使用`var`，是一个全局变量，在私有作用域外可以被访问到。（严格模式下给未经声明的变量赋值会导致错误）

这个模式和上面说到的在构造函数定义公有（特权）方法的主要区别，在于私有变量和函数是由实例共享的。特权方法是在原型上定义的，所有实例都使用这一个函数。 这个特权方法是一个闭包，总是保存着对包含作用域的引用。

```js
(function(){
  var name = '';

  Person = function(value) {
    name = value;
  }
  Person.prototype.getName = function() {
    return name;
  }
  Person.prototype.setName = function(val) {
    name = val;
  }
})();

var person1 = new Person('Nick');
person1.getName(); // 'Nick'
person.setName('Simon')
person1.getName() // 'Simon'

var person2 = new Person('Mike');
person2.getName() // 'Mike'
person1.getName() // 'Mike' 
```
如上所示，`getName()`和`setName()`方法一样，都有权访问私有变量name。name就变成了一个静态的、由所有实例共享的属性。也就是说， 在一个实例上调用`setName()`会影响所有实例。调用 `setName()`或新建一个Person实例都会赋予`name`属性一个新值。结果是所有的实例都返回相同的值。

#### 创建模块(模块模式)
**模块模式**是为单例创建私有变量和特权的方法。
> 单例： 只有一个实例的对象。

javascript是以对象字面量的方式来创建单例对象的。
```js
var singleton = {
  name: value,
  method: function() {
    // 方法代码
  }
}
```
模块模式通过为单例添加私有变量和特权方法使其得到增强，语法如下：
```js
var singleton = function(){
  // 私有变量和私有函数
  var privateVar = 10;
  function privateFn() {
    return false;
  }

  // 特权（公有）方法和属性
  return {
    publicKey: true,
    publickMethod: function() {
      privateVar++;
      return privateFn();
    }
  }
}();
```
这个模式使用了返回一个对象的匿名函数。匿名函数内部首先定义了私有变量和函数，然后返回一个字面量。这个字面变量包含可以公开的属性和方法，由于这个字面量是在匿名函数内部定义的，因此它的共有方法有权访问私有变量和函数。

### 闭包的不足
由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。过度使用闭包可能会导致内存占用过多。  需要及时释放内存，把匿名函数的引用设为 `null`。

### 一道经典的闭包面试题
```js
/**
 * 以下代码返回什么结果？
 */
function fun(n, o){
  console.log(o);
  return {
    fun: function(m){
      return fun(m, n);
    }
  }
}
var a = fun(0); a.fun(1); a.fun(2); a.fun(3);
```
- 在第一次调用fun(0)时，o为undefined；
- 第二次调用fun(1)时m为1，此时fun闭包了外层函数的n，也就是第一次调用的n=0，即m=1，n=0，并在内部调用第一层fun函数fun(1,0);所以o为0；
- 第三次调用fun(2)时m为2，但依然是调用a.fun，所以还是闭包了第一次调用时的n，所以内部调用第一层的fun(2,0);所以o为0
- 第四次同理；

所以结果是： undefined; 0; 0; 0;


```js
// 以下返回什么结果？
var b = fun(0).fun(1).fun(2).fun(3);//undefined,?,?,?
```
先从fun(0)开始看，肯定是调用的第一层fun函数；而他的返回值是一个对象，所以第二个fun(1)调用的是第二层fun函数，后面几个也是调用的第二层fun函数。

- 在第一次调用第一层fun(0)时，o为undefined；
- 第二次调用 .fun(1)时m为1，此时fun闭包了外层函数的n，也就是第一次调用的n=0，即m=1，n=0，并在内部调用第一层fun函数fun(1,0);所以o为0；
- 第三次调用 .fun(2)时m为2，此时当前的fun函数不是第一次执行的返回对象，而是第二次执行的返回对象。而在第二次执行第一层fun函数时时(1,0)所以n=1,o=0,返回时闭包了第二次的n，遂在第三次调用第三层fun函数时m=2,n=1，即调用第一层fun函数fun(2,1)，所以o为1；
- 第四次调用 .fun(3)时m为3，闭包了第三次调用的n，同理，最终调用第一层fun函数为fun(3,2)；所以o为2；

所以结果是： undefined; 0; 1; 2;

```js
// 以下返回什么结果？
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);
```
fun(0)为执行第一层fun函数，.fun(1)执行的是fun(0)返回的第二层fun函数，这里语句结束，遂c存放的是fun(1)的返回值，而不是fun(0)的返回值，所以c中闭包的也是fun(1)第二次执行的n的值。c.fun(2)执行的是fun(1)返回的第二层fun函数，c.fun(3)执行的也是fun(1)返回的第二层fun函数。根据前面两个例子，可以得知：
- 第一次调用第一层fun(0)时，o为undefined；
- 第二次调用 .fun(1)时m为1，此时fun闭包了外层函数的n，也就是第一次调用的n=0，即m=1，n=0，并在内部调用第一层fun函数fun(1,0);所以o为0；
- 第三次调用 .fun(2)时m为2，此时fun闭包的是第二次调用的n=1，即m=2，n=1，并在内部调用第一层fun函数fun(2,1);所以o为1；
- 第四次.fun(3)时同理，但依然是调用的第二次的返回值，遂最终调用第一层fun函数fun(3,1)，所以o还为1

所以结果是： undefined; 0; 1; 1;

注：此题参考文章 ：https://segmentfault.com/a/1190000004187681