# 2012-12-07-js-basic-concept-with-example

---
layout: post
title: 借助几个例子加深对js的理解
date: 2012-12-07 11:13
comments: true
categories: [js]
---
最近在weibo上看到一个关于js的帖子[So, you think you know JavaScript?](http://dmitry.baranovskiy.com/post/91403200)，正好在[这里](http://julying.com/blog/so-you-think-you-know-javascript/)看到了关于这几个题目的参考，看完之后总结如下。
<!-- more -->

#### 题目1

```
if (!("a" in window)) {
    var a = 1;
}
alert(a);
```

题目的意思是：如果a在window里面的话，将a赋值为1，然后输出a。首先需要明确几个概念：

* 所有的全局变量都属于window，那么语句var a=1；等价于window.a=1；
* 所有变量声明都在范围作用域的顶部；

对于如下代码：

```
alert("a" in window);
var a;
```

虽然a的声明在后面，但是js引擎会扫描所有变量，然后将这些变量的声明移动到顶部，那么最终的代码会变成下面这个样子：

```
var a;
alert("a" in window);
```

* js引擎只会将变量声明提前，而不会将变量赋值提前。因为将变量赋值提前可能会使代码的执行出现意想不到的结果；

所以题目中的代码最终演化为：

```
var a;
if (!("a" in window)) {
	a = 1;
}
alert(a);
```

所以最终的输出结果会是undefined。这里的“提前”可以理解为“预编译”。

#### 题目2
```
var a = 1,
    b = function a(x) {
        x && a(--x);
    };
alert(a);
```

对于这个题目，首先要知道下面几个概念：

* 函数声明和函数表达式（相当于变量赋值）的区别；

```
// 函数声明
function functionName(arg1, arg2){
    //函数体
}
// 函数表达式，赋值
var functionName = function(arg1, arg2){
    //函数体
};
```

* 函数声明也是会提前的，和变量声明一样。而函数表达式是不会提前的，和变量赋值一样；
* 函数声明会覆盖变量声明，但是不会覆盖变量赋值；为了解释这个，下面列举了两个简单例子：

```
// 函数声明和变量声明，名字相同
// 尽管变量声明在函数声明后面，但是变量声明还是被函数声明覆盖了
// 输出为function
function value(){
    return 1;
}
var value;
alert(typeof value);    //"function"

// 函数声明和变量赋值，名字相同
// 由于变量声明时进行了赋值，所有函数的声明不能覆盖变量的声明
// 所以最终输出为number
function value(){
    return 1;
}
var value = 1;
alert(typeof value);    //"number" 	
```

在我们上面的题目中，首先是变量声明加赋值，然后是一个函数表达式，变量赋值覆盖了函数声明，故而最终的输出为1。此外，如果调用b(2)，在IE下会报错，但是在其他浏览器会返回undefined。

#### 题目3

```
function a() {
    return 1；
}
var a;
alert(a);
```

有了前面的知识之后，这题很简单了，函数声明会覆盖变量声明，故而输出是函数a()的定义，如下：

```
function a() {
    return 1；
}
```

#### 题目4

```
function b(x, y, a) {
    arguments[2] = 10;
    alert(a);
}
b(1, 2, 3);
```

对于arguments，可以参见[ECMAScript arguments 对象](http://www.w3school.com.cn/js/pro_js_functions_arguments_object.asp)。活动对象是在进入函数上下文时刻被创建的，它通过函数的arguments属性初始化。arguments属性的值是Arguments对象。在函数代码中，使用特殊对象arguments，开发者无需明确指出参数名，就能访问它们。故而输出是10。

#### 题目5

```
function a() {
    alert(this);
}
a.call(null);
```

理解这个题目，必须要理解this的用法，可以参见[this](http://julying.com/blog/javascript-this/)。关于 a.call(null)；根据ECMAScript262规范规定：如果第一个参数传入的对象调用者是null或者undefined的话，call方法将把全局对象（也就是window）作为this的值。所以，不管你什么时候传入null，其this都是全局对象window，所以题目的输出就是：“[object Window]”。