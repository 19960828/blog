---
title: Js中的call、apply和bind实现
date: 2018-06-18
categories:
- JavaScript
tags:
- 面试
---

实现Js中原生的`call` `apply` 和 `bind`，有助于我们更深地理解函数式编程。

1. `apply`和`call`的实现类似。
2. `bind`实现需要考虑实例化后对原型链的影响。

<!-- more -->

实现Js中原生的`call` `apply` 和 `bind`，有助于我们更深地理解函数式编程。

1. `apply`和`call`的实现类似。
2. `bind`实现需要考虑实例化后对原型链的影响。

### 一、`call`实现

```javascript
Function.prototype.call2 = function(ctx) {
  var ctx = ctx || window; // 如果ctx为空, 指向window
  ctx.fn = this; // this指向函数本身, 作为属性添加到ctx上

  var args = [];
  for (var i = 1, len = arguments.length; i < len; i++) {
    // arguments[0]是ctx
    args.push("arguments[" + i + "]");
  }
  // 此时: args是 [ 'arguments[1]', 'arguments[2]' ], Array类型

  var result = eval("ctx.fn(" + args + ")");
  // 字符串拼接, args自动调用 Array.toString() 进行转化
  // eval括号内执行的命令: ctx.fn(arguments[1],arguments[2])

  delete ctx.fn; // 使用后删除属性, 不干扰原来的对象
  return result;
};

var val = 2;
var obj = {
  val: 1
};

function func(name, age) {
  console.log("val is", this.val, "name is", name, "age is", age);
}

func.call(obj, "yuanxin", 20); // 原生的call
func.call2(obj, "yuanxin", 20); // 模拟的call
```

### 二、`apply`实现

> 和`call`不同的是：`apply`的传入形式是`Array`。

```javascript
Function.prototype.apply2 = function(ctx, arr) {
  var ctx = ctx || window;
  ctx.fn = this;

  var result;
  if (!arr) {
    result = ctx.fn();
  } else {
    var args = [];
    for (var i = 0, len = arr.length; i < len; i++) {
      // 从index = 0 开始
      args.push("arr[" + i + "]");
    }
    result = eval("ctx.fn(" + args + ")");
  }
  delete ctx.fn;
  return result;
};

var val = 2;
var obj = {
  val: 1
};

function func(name, age) {
  console.log("val is", this.val, "name is", name, "age is", age);
}

func.apply(obj, ["yuanxin", 20]);
func.apply2(obj, ["yuanxin", 20]);
```

### 三、`bind`实现

```javascript
Function.prototype.bind2 = function(ctx) {
  // 绑定的对象保存在ctx中
  var that = this; // 函数对象本身保存在that中
  var args = Array.prototype.slice.call(arguments, 1);
  return function() {
    var bindArgs = Array.prototype.slice.call(arguments);
    that.apply(ctx, args.concat(bindArgs));
  };
};

var obj = {
  val: 1
};

function parent(name, age) {
  console.log("val is", this.val, "name is", name, "age is", age);
}

var children = parent.bind(obj, "yuanxin");
children(23);

var children2 = parent.bind2(obj, "yuanxin");
children2(23);
```

> `bind`运算符返回的函数对象，可以通过`new`操作符来创建新对象。此时，需要处理原来的原型链。

```javascript
Function.prototype.bind3 = function(ctx) {
  var that = this;
  var args = Array.prototype.slice.call(arguments, 1);
  var bound = function() {
    var bindArgs = Array.prototype.slice.call(arguments);
    // that 指向绑定函数, 通过 instanceof 判断 this是否在that原型链上
    // 当作为构造函数时候, this指向实例
    // 当作为普通函数时候, this指向window(浏览器中). 此时, this.prototype 为 undefined
    that.apply(this instanceof that ? this : ctx, args.concat(bindArgs));
  };
  bound.prototype = this.prototype; // 为函数绑定原型链
  return bound;
};

var obj = {
  val: 1
};

function parent(name, age) {
  this.role = "parent";
  console.log("val is", this.val, "name is", name, "age is", age);
  console.log(this.role, this.address);
}
parent.prototype.address = "China";

var children = parent.bind3(obj, "yuanxin");
// children(18);
var childrenObj = new children("18");
// 通过new构造函数，bind绑定的对象会失效
// 所以，this.val 输出 undefined
```