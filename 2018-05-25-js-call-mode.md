---
title: Js调用模式
date: 2018-05-25
categories:
- JavaScript
tags:
- 设计模式
---

> 在`js`中，一共4中调用方式。需要注意的是，调用方式中，`this`的指向问题。

- 函数调用模式
- 方法调用模式
- 构造器调用模式
- `apply`调用模式

<!-- more -->

> 在`js`中，一共4中调用方式。需要注意的是，调用方式中，`this`的指向问题。

- 函数调用模式
- 方法调用模式
- 构造器调用模式
- `apply`调用模式

### 函数调用模式
> `this`丢失，debug会提示未定义相应属性。按照规范，需要将`this`赋值给`that`

```javascript
let myObj = {
    value : 1,
    double : function(){
        let that = this
        let _ = function (){
            that.value = that.value *2
            // this.value = this.value *2 // 内部函数的this被绑定错误值
        }
        _() // 函数调用模式
    }
}

myObj.double() // 方法调用模式
console.log(myObj.value) // 输出2
```

### 方法调用模式
> 在*函数调用模式*中的`myObj.double()`就是方法调用模式，这时，这个**函数被保存为对象的一个属性**。

### 构造器调用模式
> 通过`new`来声明，`this`会被绑定到一个连接`prototype`的新对象。结合原型链，可以实现很多有趣用法。

```javascript
function Myobj(value){ // 注意命名规范
    this.value = value
}

Myobj.prototype.getValue = function(){
    return this.value
}

let myobj = new Myobj(1) // 构造器调用
console.log(myobj.getValue())
```

### `apply`调用模式
> `apply`:为了动态改变`this`而出现。**结合闭包，可以更方便的实现原型继承**

基于上面的例子，我们首先将`getValue`方法放入单独的文件`./util.js`下。

**./util.js**

```javascript
exports =  module.exports = function getValue(arg){
    return `${this.value} \n我是参数:${arg} `
}
```

然后再在需要的文件中引入：

```javascript
'use strict'
function Myobj(value){
    this.value = value
}
Myobj.prototype.getValue = function(arg){
    return require('./util').apply(this,[arg]) // apply调用模式
}

let myobj = new Myobj(1)
console.log(myobj.getValue(-1))
```

**如此，优美地实现了组件化，代码逻辑和架构变得更加清晰。**

#### 关于`apply`和`call`
相同点：
- 作用一样：动态改变`this`

不同点：
- `apply`调用方式：`func.apply(obj,[arg1,arg2,...])`，接受**数组参数**
- `call`调用方式：`func.call(obj,arg1,arg2,...)`，接受**连续参数**