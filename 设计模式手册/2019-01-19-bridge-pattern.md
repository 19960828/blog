---
title: "设计模式手册之桥接模式"
date: "2019-01-19"
categories: 设计模式手册
tags:
  - 设计模式
  - Python
  - ES6
permalink: "2019-01-19-bridge-pattern"
---

在封装开源库的组件时候，经常会用到这种设计模式。

例如，对外提供暴露一个`afterFinish`函数, 
如果用户有传入此函数, 那么就会在某一段代码逻辑中调用。

这个过程中，组件起到了“桥”的作用，而具体实现是用户自定义。

<!-- more -->

## 1. 什么是桥接模式

> 桥接模式将抽象部分和具体实现部分**分离**，两者可独立变化，也可以一起工作。

在这种模式的实现上，需要一个对象担任**桥**的角色，起到连接的作用。

## 2. 应用场景

在封装开源库的组件时候，经常会用到这种设计模式。

例如，对外提供暴露一个`afterFinish`函数, 
如果用户有传入此函数, 那么就会在某一段代码逻辑中调用。

这个过程中，组件起到了“桥”的作用，而具体实现是用户自定义。

## 3. 多语言实现

### 3.1 ES6 实现

JavaScript中桥接模式的典型应用是：`Array`对象上的`forEach`函数。

此函数负责循环遍历数组每个元素，是抽象部分；
而回调函数`callback`就是具体实现部分。

下方是模拟`forEach`方法：

```javascript
const forEach = (arr, callback) => {
  if (!Array.isArray(arr)) return;

  const length = arr.length;
  for (let i = 0; i < length; ++i) {
    callback(arr[i], i);
  }
};

// 以下是测试代码
let arr = ["a", "b"];
forEach(arr, (el, index) => console.log("元素是", el, "位于", index));
```

### 3.2 python3 实现

和Js一样，这里也是模拟一个`for_each`函数：
它会循环遍历所有的元素，并且对每个元素执行指定的函数。

```python
from inspect import isfunction

# for_each 起到了“桥”的作用
def for_each(arr, callback):
  if isinstance(arr, list) == False or isfunction(callback) == False:
    return
  
  for (index, item) in enumerate(arr):
    callback(item, index)

# 具体实现部分
def callback(item, index):
  print('元素是', item, '; 它的位置是', index)

# 以下是测试代码
if __name__ == '__main__':
  arr = ['a', 'b']
  
  for_each(arr, callback)
```