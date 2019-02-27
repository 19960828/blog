---
title: 详解promise、async和await的执行顺序
date: 2018-05-29
categories:
- ES6
tags:
- 面试
---

> 今日头条的笔试题，请问打印/输出顺序是什么？

**超级好题，可以加深对`ES6`核心概念的理解哦。**

```javascript
async function async1(){
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
async function async2(){
  console.log('async2')
}
console.log('script start')
setTimeout(function(){
  console.log('setTimeout') 
},0)  
async1();
new Promise(function(resolve){
  console.log('promise1')
  resolve();
}).then(function(){
  console.log('promise2')
})
console.log('script end')
```

<!-- more -->

## 1、题目和答案

> 一道题题目：下面这段promise、async和await代码，请问控制台打印的顺序？

```javascript
async function async1(){
  console.log('async1 start')
  await async2()
  console.log('async1 end')
}
async function async2(){
  console.log('async2')
}
console.log('script start')
setTimeout(function(){
  console.log('setTimeout') 
},0)  
async1();
new Promise(function(resolve){
  console.log('promise1')
  resolve();
}).then(function(){
  console.log('promise2')
})
console.log('script end')
```

上述，在`Chrome 66`和`node v10`中，正确输出是：

```shell
script start
async1 start
async2
promise1
script end
promise2
async1 end
setTimeout
```

## 2、知识点

显然，这考察的是js中的事件循环和回调队列。注意以下几点：
- `Promise`优先于`setTimeout`宏任务。所以，`setTimeout`回调会在最后执行。
- `Promise`一旦被定义，就会立即执行。
- `Promise`的`reject`和`resolve`是异步执行的回调。所以，`resolve()`会被放到回调队列中，在主函数执行完和`setTimeout`前调用。
- `await`执行完后，会让出线程。`async`标记的函数会返回一个`Promise`对象

## 3、 难点
> 最令人困惑的，就是`async1 end`在`promise2`之后输出

在函数`async1`中，执行`promise`（*由于`async2`是`async`标记的函数，所以默认返回`promise`对象*）会发现`resolve()`，然后放入回调队列。

接着执行下方的`new Promise`中的`resolve()`输出`promise2`，再回来输出`async1 end`。

其中，`async1`函数可以写成以下方式（便于理解）：

```javascript
async function async1(){
  console.log('async1 start')
  async2().then( _ => {
    console.log( 'async1 end ')
  })
}
```


## 3、流程

1. `console.log('script start')`输出：`script start`
2. `setTimeout`被放在最后调用
3. 执行`async1`函数，输出`async1 start`。然后，进入`async2`函数，输出`async2`，并返回`Promise`对象。回到`async1`，由于`await`，让出线程，`async2`函数返回的`Promise`放在**回调队列**。
4. 新new了一个`Promise`对象，输出`promise1`。其中的`resolve()`被放在回调队列。
5. `console.log('script end')`输出：`script end`
6. 执行回调队列中，`async1`返回的`Promise`对象，对象产生的`resolve`被放入对调队列。这里不输出任何值。
7. 执行回调队列中，下方`Promise`显式声明的`resolve`，输出`promise2`。
8. 执行回调队列中，由于`async1`函数返回的`promise`对象的`resolve`，输出`async1 end`。
9. 执行回调队列中，最后的`setTimeout`，输出`setTimeout`
10. finish

## 4、参考

1. [promise、async和await之执行顺序的那点事](https://segmentfault.com/a/1190000015057278?utm_source=index-hottest)
2. [半年工作经验今日头条和美团面试题面经分享](https://juejin.im/post/5b03e79951882542891913e8)
3. [关于node和chrome运行结果不一样的详解](https://www.zhihu.com/question/268007969)
