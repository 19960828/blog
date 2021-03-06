---
title: "设计模式手册之装饰者模式"
date: "2019-01-12"
categories: 设计模式手册
tags:
  - 设计模式
  - Python
  - ES6
permalink: "2019-01-12-decorator-pattern"
---

> 装饰者模式：在**不改变**对象自身的基础上，**动态**地添加功能代码。

根据描述，装饰者显然比继承等方式更灵活，而且**不污染**原来的代码，代码逻辑松耦合。

<!-- more -->

## 1. 什么是“装饰者模式”？

> 装饰者模式：在**不改变**对象自身的基础上，**动态**地添加功能代码。  

根据描述，装饰者显然比继承等方式更灵活，而且**不污染**原来的代码，代码逻辑松耦合。

## 2. 应用场景

装饰者模式由于松耦合，多用于一开始不确定对象的功能、或者对象功能经常变动的时候。
尤其是在**参数检查**、**参数拦截**等场景。

## 3. 代码实现

### 3.1 ES6 实现

ES6的装饰器语法规范只是在“提案阶段”，而且**不能**装饰普通函数或者箭头函数。

下面的代码，`addDecorator`可以为指定函数增加装饰器。

其中，装饰器的触发可以在函数运行之前，也可以在函数运行之后。

**注意**：装饰器需要保存函数的运行结果，并且返回。

```javascript
const addDecorator = (fn, before, after) => {
  let isFn = fn => typeof fn === "function";

  if (!isFn(fn)) {
    return () => {};
  }

  return (...args) => {
    let result;
    // 按照顺序执行“装饰函数”
    isFn(before) && before(...args);
    // 保存返回函数结果
    isFn(fn) && (result = fn(...args));
    isFn(after) && after(...args);
    // 最后返回结果
    return result;
  };
};

/******************以下是测试代码******************/

const beforeHello = (...args) => {
  console.log(`Before Hello, args are ${args}`);
};

const hello = (name = "user") => {
  console.log(`Hello, ${name}`);
  return name;
};

const afterHello = (...args) => {
  console.log(`After Hello, args are ${args}`);
};

const wrappedHello = addDecorator(hello, beforeHello, afterHello);

let result = wrappedHello("godbmw.com");
console.log(result);
```

### 3.2 Python3 实现

python直接提供装饰器的语法支持。用法如下：

```python
# 不带参数
def log_without_args(func):
    def inner(*args, **kw):
        print("args are %s, %s" % (args, kw))
        return func(*args, **kw)
    return inner

# 带参数
def log_with_args(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print("decorator's arg is %s" % text)
            print("args are %s, %s" % (args, kw))
            return func(*args, **kw)
        return wrapper
    return decorator

@log_without_args
def now1():
    print('call function now without args')

@log_with_args('execute')
def now2():
    print('call function now2 with args')

if __name__ == '__main__':
    now1()
    now2()
```

其实python中的装饰器的实现，也是通过“闭包”实现的。

以上述代码中的`now1`函数为例，装饰器与下列语法等价：

```python
# ....
def now1():
    print('call function now without args')
# ... 
now_without_args = log_without_args(now1) # 返回被装饰后的 now1 函数
now_without_args() # 输出与前面代码相同
```


## 4. 参考

- [JavaScript Decorators: What They Are and When to Use Them](https://www.sitepoint.com/javascript-decorators-what-they-are/)
- [《阮一峰ES6-Decorator》](http://es6.ruanyifeng.com/#docs/decorator)
- [《廖雪峰python-Decorator》](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014318435599930270c0381a3b44db991cd6d858064ac0000)