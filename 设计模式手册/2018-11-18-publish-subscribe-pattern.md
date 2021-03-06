---
title: "设计模式手册之订阅-发布模式"
date: 2018-11-18
categories: 设计模式手册
tags:
- 设计模式
- Python
- ES6
permalink: "2018-11-18-publish-subscribe-pattern"
---

> 订阅-发布模式定义了对象之间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖它的对象都可以得到通知。

了解过事件机制或者函数式编程的朋友，应该会体会到“订阅-发布模式”所带来的“**时间解耦**”和“**空间解耦**”的优点。借助函数式编程中闭包和回调的概念，可以很优雅地实现这种设计模式。

<!-- more -->

## 1. 什么是“订阅-发布模式”？

> 订阅-发布模式定义了对象之间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖它的对象都可以得到通知。

了解过事件机制或者函数式编程的朋友，应该会体会到“订阅-发布模式”所带来的“**时间解耦**”和“**空间解耦**”的优点。借助函数式编程中闭包和回调的概念，可以很优雅地实现这种设计模式。

## 2. “订阅-发布模式” vs 观察者模式

订阅-发布模式和观察者模式概念相似，但在订阅-发布模式中，订阅者和发布者之间多了一层中间件：一个被抽象出来的信息调度中心。

但其实没有必要太深究2者区别，因为《Head First 设计模式》这本经典书都写了：**发布+订阅=观察者模式**。**其核心思想是状态改变和发布通知。**在此基础上，根据语言特性，进行实现即可。

## 3. 代码实现

### 3.1 python3实现

python中我们定义一个事件类`Event`, 并且为它提供 事件监听函数、（事件完成后）触发函数，以及事件移除函数。任何类都可以通过继承这个通用事件类，来实现“订阅-发布”功能。

```python
class Event:
  def __init__(self):
    self.client_list = {}
  
  def listen(self, key, fn):
    if key not in self.client_list:
      self.client_list[key] = []
    self.client_list[key].append(fn)
  
  def trigger(self, *args, **kwargs):
    fns = self.client_list[args[0]]

    length = len(fns)
    if not fns or length == 0:
      return False
    
    for fn in fns:
      fn(*args[1:], **kwargs)
    
    return False

  def remove(self, key, fn):
    if key not in self.client_list or not fn:
      return False
    
    fns = self.client_list[key]
    length = len(fns)

    for _fn in fns:
      if _fn == fn:
        fns.remove(_fn)
    
    return True

# 借助继承为对象安装 发布-订阅 功能
class SalesOffice(Event):
  def __init__(self):
    super().__init__()

# 根据自己需求定义一个函数：供事件处理完后调用
def handle_event(event_name):
  def _handle_event(*args, **kwargs):
    print("Price is", *args, "at", event_name)
  
  return _handle_event


if __name__ == "__main__":
  # 创建2个回调函数
  fn1 = handle_event("event01")
  fn2 = handle_event("event02")

  sales_office = SalesOffice()

  # 订阅event01 和 event02 这2个事件，并且绑定相关的 完成后的函数
  sales_office.listen("event01", fn1)
  sales_office.listen("event02", fn2)

  # 当两个事件完成时候，触发前几行绑定的相关函数
  sales_office.trigger("event01", 1000)
  sales_office.trigger("event02", 2000)

  sales_office.remove("event01", fn1)

  # 打印：False
  print(sales_office.trigger("event01", 1000))
```

### 3.2 ES6 实现

JS中一般用事件模型来代替传统的发布-订阅模式。任何一个对象的原型链被指向`Event`的时候，这个对象便可以绑定自定义事件和对应的回调函数。

```javascript
const Event = {
  clientList: {},

  // 绑定事件监听
  listen(key, fn){
    if(! this.clientList[key]){
      this.clientList[key] = [];
    }
    this.clientList[key].push(fn);
    return true;
  },

  // 触发对应事件
  trigger(){
    const key = Array.prototype.shift.apply(arguments),
      fns = this.clientList[key];
    
      if(!fns || fns.length === 0){
        return false;
      }

      for(let fn of fns){
        fn.apply(null, arguments);
      }

      return true;
  },

  // 移除相关事件
  remove(key, fn){
    let fns = this.clientList[key];

    // 如果之前没有绑定事件
    // 或者没有指明要移除的事件
    // 直接返回
    if(!fns || !fn){
      return false;
    }
    
    // 反向遍历移除置指定事件函数
    for(let l = fns.length - 1; l >= 0; l--){
      let _fn = fns[l];
      if(_fn === fn){
        fns.splice(l, 1);
      }
    }

    return true;
  }
}

// 为对象动态安装 发布-订阅 功能
const installEvent = (obj) => {
  for(let key in Event){
    obj[key] = Event[key];
  }
}

let salesOffices = {};
installEvent(salesOffices);

// 绑定自定义事件和回调函数

salesOffices.listen("event01", fn1 = (price) => {
  console.log("Price is", price, "at event01");
})

salesOffices.listen("event02", fn2 = (price) => {
  console.log("Price is", price, "at event02");
})

salesOffices.trigger("event01", 1000);
salesOffices.trigger("event02", 2000);

salesOffices.remove("event01", fn1);

// 输出: false
// 说明删除成功
console.log(salesOffices.trigger("event01", 1000));
```


## 4. 参考

- [维基百科·订阅-发布模式](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)
- [观察者模式和订阅-发布模式的不同](https://www.zhihu.com/question/23486749)
- 《JavaScript 设计模式和开发实践》