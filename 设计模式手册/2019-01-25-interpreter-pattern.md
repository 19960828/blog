---
title: "设计模式手册之解释器模式"
date: "2019-01-25"
categories: 设计模式手册
tags:
  - 设计模式
  - ES6
permalink: "2019-01-25-interpreter-pattern"
---

> 解释器模式: 提供了评估语言的**语法**或**表达式**的方式。

实现这种模式的**核心**是：
1. 抽象表达式：主要有一个`interpret()`操作
  - 终结符表达式：`R = R1 + R2`中，`R1` `R2`就是终结符
  - 非终结符表达式：`R = R1 - R2`中，`-`就是终结符
2. 环境(Context): **存放**文法中各个**终结符**所对应的具体值。比如前面`R1`和`R2`的值。

<!-- more -->

## 1. 什么是“解释器模式？

> 解释器模式: 提供了评估语言的**语法**或**表达式**的方式。

这是基本**不怎么使用**的一种设计模式。
确实想不到什么场景一定要用此种设计模式。

实现这种模式的**核心**是：
1. 抽象表达式：主要有一个`interpret()`操作
  - 终结符表达式：`R = R1 + R2`中，`R1` `R2`就是终结符
  - 非终结符表达式：`R = R1 - R2`中，`-`就是终结符
2. 环境(Context): **存放**文法中各个**终结符**所对应的具体值。比如前面`R1`和`R2`的值。

## 2. 优缺点

**优点**显而易见，每个**文法规则**可以表述为一个类或者方法。
这些文法互相不干扰，符合“开闭原则”。

由于每条文法都需要构建一个类或者方法，文法数量上去后，**很难维护**。
并且，语句的执行**效率低**（一直在不停地互相调用）。

## 3. 多语言实现

### 3.1 ES6 实现

为了方便说明，下面省略了“抽象表达式”的实现。

```javascript
class Context {
  constructor(){
    this._list = []; // 存放 终结符表达式
    this._sum = 0; // 存放 非终结符表达式(运算结果)
  }
  
  get sum(){
    return this._sum;
  }

  set sum(newValue){
    this._sum = newValue;
  }

  add(expression){
    this._list.push(expression);
  }

  get list(){
    return [...this._list];
  }
}

class PlusExpression {
  interpret(context) {
    if(!(context instanceof Context)) {
      throw new Error('TypeError');
    } 
    context.sum = ++context.sum;
  }
}

class MinusExpression {
  interpret(context) {
    if(!(context instanceof Context)) {
      throw new Error('TypeError');
    } 
    context.sum = --context.sum;
  }
}

/** 以下是测试代码 **/

const context = new Context();

// 依次添加: 加法 | 加法 | 减法 表达式
context.add(new PlusExpression());
context.add(new PlusExpression());
context.add(new MinusExpression());

// 依次执行: 加法 | 加法 | 减法 表达式
context.list.forEach(expression => expression.interpret(context));
console.log(context.sum);
```

## 4. 参考

- [菜鸟教程--解释器模式](http://www.runoob.com/design-pattern/interpreter-pattern.html)
- [@工匠若水](https://blog.csdn.net/yanbober/article/details/45537601)