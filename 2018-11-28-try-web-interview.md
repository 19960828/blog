---
title: "刷《一年半经验，百度、有赞、阿里面试总结》·手记"
date: 2018-11-28
categories:
- JavaScript
tags:
- 面试
- 网络协议
- HTML5
- 算法
---

> 在掘金上看到了一位大佬发了一篇很详细的面试记录文章-[《一年半经验，百度、有赞、阿里面试总结》](https://juejin.im/post/5befeb5051882511a8527dbe)，为了查漏补缺，抽空就详细做了下。（*估计只有我这么无聊了哈哈哈*）

原文没有给出的或者有些不完善的答案，也尽力给出/完善了（可能有错，大家自行辨别）。有些很困难的题目（例如实现`Promise`），附带相关链接（懒癌患者福利）。

**总的来说，将这些题目分成了“Javascript”、“CSS”、“浏览器/协议”、“算法”和“Web工程化”5个部分进行回答和代码实现。**

<!-- more -->

> 在掘金上看到了一位大佬发了一篇很详细的面试记录文章-[《一年半经验，百度、有赞、阿里面试总结》](https://juejin.im/post/5befeb5051882511a8527dbe)，为了查漏补缺，抽空就详细做了下。（*估计只有我这么无聊了哈哈哈*）

有给出的或者有些不完善的答案，也尽力给出/完善了（可能有错，大家自行辨别）。有些很困难的题目（例如实现`Promise`），附带相关链接（懒癌患者福利）。

**总的来说，将这些题目分成了“Javascript”、“CSS”、“浏览器/协议”、“算法”和“Web工程化”5个部分进行回答和代码实现。**

## 1. Javascript相关

#### 1.1 回文字符串

> 题目：实现一个函数，判断是不是回文字符串

原文的思路是将字符串转化成数组=>反转数组=>拼接成字符串。**这种做法充分利用了js的BIF，但性能有所损耗**：

```javascript
function run(input) {
  if (typeof input !== 'string') return false;
  return input.split('').reverse().join('') === input;
}
```

其实正常思路也很简单就能实现,**性能更高，但是没有利用js的特性**：
```javascript
// 回文字符串
const palindrome = (str) => {
  // 类型判断
  if(typeof str !== 'string') {
    return false;
  }

  let len = str.length;
  for(let i = 0; i < len / 2; ++i){
    if(str[i] !== str[len - i - 1]){
      return false;
    }
  }
  return true;
}
```

#### 1.2 实现`Storage`

> 题目：实现Storage，使得该对象为单例，并对localStorage进行封装设置值setItem(key,value)和getItem(key)

题目重点是**单例模式**，需要注意的是借助`localStorage`，不是让自己手动实现！

*感谢[@whiteyork_](https://juejin.im/user/57b68c2dc4c97100557f5b9c)和[@chenzesam](https://juejin.im/user/582733cbda2f600056d5f067)的提醒：箭头函数没有`prototype`*

```javascript
function Storage(){}

Storage.getInstance = (function(){
  var instance = null
  return function(){
    if(!instance){
      instance = new Storage()
    }
    return instance
  }
})()

Storage.prototype.setItem = function(key, value) {
  return localStorage.setItem(key, value)
}

Storage.prototype.getItem = function(key){
  return localStorage.getItem(key)
}

// 测试代码：Chrome环境
let a = Storage.getInstance()
let b = Storage.getInstance()
console.log(a === b)

a.setItem("key", 1)
console.log(b.getItem("key"))
```

#### 1.3 JS事件流

> 题目：说说事件流吧

事件流分为冒泡和捕获。

事件冒泡：子元素的触发事件会一直向父节点传递，一直到根结点停止。此过程中，可以在每个节点捕捉到相关事件。可以通过`stopPropagation`方法终止冒泡。

事件捕获：和“事件冒泡”相反，从根节点开始执行，一直向子节点传递，直到目标节点。~~印象中只有少数浏览器的老旧版本才是这种事件流，可以忽略。~~这里说的确实有问题，更正下：**`addEventLister`给出了第三个参数同时支持冒泡与捕获。**

*感谢[@junior-yang](https://juejin.im/user/5be944636fb9a04a102ecf22)的提醒*

#### 1.4 实现函数继承

> 题目：现在有一个函数A和函数B，请你实现B继承A。并且说明他们优缺点。

**方法一：绑定构造函数**

优点：可以实现多继承

缺点：不能继承父类原型方法/属性

```javascript
function Animal(){
  this.species = "动物";
}

function Cat(){
  Animal.apply(this, arguments); // 父对象的构造函数绑定到子节点上
}

var cat = new Cat()
console.log(cat.species) // 输出：动物
```

**方法二：原型链继承**

优点：能够继承父类原型和实例方法/属性，并且可以捕获父类的原型链改动

缺点：无法实现多继承，会浪费一些内存（`Cat.prototype.constructor = Cat`）。**除此之外，需要注意应该将`Cat.prototype.constructor`重新指向本身。**

**js中交换原型链，均需要修复`prototype.constructor`指向问题。**

```javascript
function Animal(){
  this.species = "动物";
}
Animal.prototype.func = function(){
  console.log("heel")
}

function Cat(){}
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat

var cat = new Cat()
console.log(cat.func, cat.species)
```

**方法3:结合上面2种方法**

```javascript
function Animal(){
  this.species = "动物";
}
Animal.prototype.func = function(){
  console.log("heel")
}

function Cat(){
  Animal.apply(this, arguments)
}
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat;

var cat = new Cat()
console.log(cat.func, cat.species)
```









#### 1.5 ES5对象 vs ES6对象

> 题目：es6 class 的new实例和es5的new实例有什么区别？

在`ES6`中（和`ES5`相比），`class`的`new`实例有以下特点：

- `class`的构造参数必须是`new`来调用，不可以将其作为普通函数执行
- `es6` 的`class`不存在变量提升
- **最重要的是：`es6`内部方法不可以枚举**。es5的`prototype`上的方法可以枚举。

为此我做了以下测试代码进行验证：
```javascript
console.log(ES5Class()) // es5:可以直接作为函数运行
// console.log(new ES6Class()) // 会报错：不存在变量提升

function ES5Class(){
  console.log("hello")
}

ES5Class.prototype.func = function(){ console.log("Hello world") }

class ES6Class{
  constructor(){}
  func(){
    console.log("Hello world")
  }
}

let es5 = new ES5Class()
let es6 = new ES6Class()

console.log("ES5 :")
for(let _ in es5){
  console.log(_)
}

// es6:不可枚举
console.log("ES6 :")
for(let _ in es6){
  console.log(_)
}
```

这篇[《JavaScript创建对象—从es5到es6》](https://fed.renren.com/2017/08/07/js-oop-es52es6/)对这个问题的深入解释很好，推荐观看！

#### 1.6 实现`MVVM`

> 题目：请简单实现双向数据绑定mvvm

**vuejs是利用`Object.defineProperty`来实现的MVVM，采用的是订阅发布模式。**每个`data`中都有set和get属性，这种点对点的效率，比`Angular`实现MVVM的方式的效率更高。

```html
<body>
  <input type="text">
  <script>
    const input = document.querySelector('input')
    const obj = {}

    Object.defineProperty(obj, 'data', {
      enumerable: false,  // 不可枚举
      configurable: false, // 不可删除
      set(value){
        input.value = value
        _value = value
        // console.log(input.value)
      },
      get(){
        return _value
      }
    }) 
    obj.data = '123'
    input.onchange = e => {
      obj.data = e.target.value
    }
  </script>
</body>
```

#### 1.7 实现`Promise`

这是一位大佬实现的`Promise`版本：**过了`Promie/A+`标准的测试**！！！**网上能搜到的基本都是从这篇文章变形而来或者直接照搬**！！！原文地址，直接戳：[剖析Promise内部结构，一步一步实现一个完整的、能通过所有Test case的Promise类](https://github.com/xieranmaya/blog/issues/3)

下面附上一种近乎完美的实现：可能无法和其他`Promise`库的实现无缝对接。但是，上面的原文实现了全部的，欢迎Mark！
```javascript
function MyPromise(executor){
  var that = this
  this.status = 'pending' // 当前状态
  this.data = undefined
  this.onResolvedCallback = [] // Promise resolve时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面
  this.onRejectedCallback = [] // Promise reject时的回调函数集，因为在Promise结束之前有可能有多个回调添加到它上面

  // 更改状态 => 绑定数据 => 执行回调函数集
  function resolve(value){
    if(that.status === 'pending'){
      that.status = 'resolved'
      that.data = value
      for(var i = 0; i < that.onResolvedCallback.length; ++i){
        that.onResolvedCallback[i](value)
      }
    }
  }

  function reject(reason){
    if(that.status === 'pending'){
      that.status = 'rejected'
      that.data = reason
      for(var i = 0; i < that.onResolvedCallback.length; ++i){
        that.onRejectedCallback[i](reason)
      }
    }
  }

  try{ 
    executor(resolve, reject) // resolve, reject两个函数可以在外部传入的函数（executor）中调用
  } catch(e) { // 考虑到执行过程可能有错
    reject(e)
  }
}

// 标准是没有catch方法的，实现了then，就实现了catch
// then/catch 均要返回一个新的Promise实例

MyPromise.prototype.then = function(onResolved, onRejected){
  var that = this
  var promise2

  // 值穿透
  onResolved = typeof onResolved === 'function' ? onResolved : function(v){ return v }
  onRejected = typeof onRejected === 'function' ? onRejected : function(r){ return r }

  if(that.status === 'resolved'){
    return promise2 = new MyPromise(function(resolve, reject){
      try{
        var x = onResolved(that.data)
        if(x instanceof MyPromise){ // 如果onResolved的返回值是一个Promise对象，直接取它的结果做为promise2的结果
          x.then(resolve, reject)
        }
        resolve(x) // 否则，以它的返回值做为promise2的结果 
      } catch(e) {
        reject(e) // 如果出错，以捕获到的错误做为promise2的结果
      }
    })
  }

  if(that.status === 'rejected'){
    return promise2 = new MyPromise(function(resolve, reject){
      try{
        var x = onRejected(that.data)
        if(x instanceof MyPromise){
          x.then(resolve, reject)
        }
      } catch(e) {
        reject(e)
      }
    })
  }

  if(that.status === 'pending'){
    return promise2 = new MyPromise(function(resolve, reject){
      self.onResolvedCallback.push(function(reason){
        try{
          var x = onResolved(that.data)
          if(x instanceof MyPromise){
            x.then(resolve, reject)
          }
        } catch(e) {
          reject(e)
        }
      })

      self.onRejectedCallback.push(function(value){
        try{
          var x = onRejected(that.data)
          if(x instanceof MyPromise){
            x.then(resolve, reject)
          }
        } catch(e) {
          reject(e)
        }
      })
    })
  }
}

MyPromise.prototype.catch = function(onRejected){
  return this.then(null, onRejected)
}

// 以下是简单的测试样例：
new MyPromise(resolve => resolve(8)).then(value => {
  console.log(value)
})
```


#### 1.8 Event Loop

> 题目：说一下JS的EventLoop

其实阮一峰老师这篇[《JavaScript 运行机制详解：再谈Event Loop》](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)已经讲的很清晰了（手动赞）！

这里简单总结下：

1. JS是单线程的，其上面的所有任务都是在两个地方执行：**执行栈和任务队列**。前者是存放同步任务；后者是异步任务有结果后，就在其中放入一个事件。
2. 当执行栈的任务都执行完了（栈空），js会读取任务队列，并将可以执行的任务从任务队列丢到执行栈中执行。
3. 这个过程是循环进行，所以称作`Loop`。

## 2. CSS相关

#### 2.1 水平垂直居中

> 题目： 两种以上方式实现已知或者未知宽度的垂直水平居中

第一种方法就是利用`CSS3`的`translate`进行偏移定位，**注意：两个参数的百分比都是针对元素本身计算的。**

```css
.wrap {
  position: relative;
  width: 100vw;
  height: 100vh;
}

.box {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

第二种方法是利用`CSS3`的`flex`布局，父元素diplay属性设置为`flex`，并且定义元素在两条轴线的布局方式均为`center`

```css
.wrap {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
}

.wrap .box {
  width: 100px;
  height: 100px;
}
```

第三种方法是利用**margin负值**来进行元素偏移，优点是浏览器兼容好，缺点是不够灵活（要自行计算margin的值）：

```css
.wrap {
  position: relative;
  width: 100vw;
  height: 100vh;
}

.box {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100px;
  height: 100px;
  margin: -50px 0 0 -50px;
}
```

#### 2.2 “点击”改变样式

> 题目：实现效果，点击容器内的图标，图标边框变成border 1px solid red，点击空白处重置。

利用`event.target`可以判断是否是指定元素本身（判断“空白处”），除此之外，注意禁止冒泡（题目指明了“容器内”）。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
  #app {
    min-width: 100vw;
    min-height: 100vh;
  }
  #app .icon{
    display: inline-block;
    cursor: pointer;
  }
  </style>
</head>
<body>
  <div id="app">
    <span class="icon">123456</span>
  </div>
  <script>
  const app = document.querySelector("#app")
  const icon = document.querySelector(".icon")

  app.addEventListener("click", e => {
    if(e.target === icon){
      return;
    }
    // 非空白处才去除 border 
    icon.style.border = "none";
  })

  icon.addEventListener("click", e => {
    // 禁止冒泡
    e.stopPropagation()
    // 更改样式
    icon.style.border = "1px solid red";
  })
  </script>
</body>
</html>
```

## 3. 浏览器/协议相关

#### 3.1 缓存机制

> 题目：说一下浏览器的缓存机制。

浏览器缓存**分为强缓存和协商缓存**。缓存的作用是**提高客户端速度、节省网络流量、降低服务器压力**。

强缓存：浏览器请求资源，如果header中的`Cache-Control`和`Expires`没有过期，直接从缓存（本地）读取资源，不需要再向服务器请求资源。

协商缓存：浏览器请求的资源如果是过期的，那么会向服务器发送请求，header中带有`Etag`字段。服务器再进行判断，如果ETag匹配，则返回给客户端300系列状态码，客户端继续使用本地缓存；否则，客户端会重新获取数据资源。

关于过程中详细的字段，可以参考这篇《[http协商缓存VS强缓存](https://segmentfault.com/a/1190000008956069)》

#### 3.2 从URL到页面生成

>  题目：输入URL到看到页面发生的全过程，越详细越好

1. DNS解析
2. 建立TCP连接（3次握手）
3. 发送HTTP请求，从服务器下载相关内容
4. 浏览器构建DOM树和CSS树，然后生成渲染树。这个一个渐进式过程，引擎会力求最快将内容呈现给用户。
5. 在第四步的过程中，`<script>`的位置和加载方式会影响响应速度。
6. 搞定了，关闭TCP连接（4次握手）

#### 3.3 TCP握手

> 题目：解释TCP建立的时候的3次握手和关闭时候的4次握手

看这题的时候，我也是突然懵（手动捂脸）。推荐翻一下计算机网络的相关书籍，对于`FIN`、`ACK`等字段的讲解很赞！

#### 3.4 CSS和JS位置

> 题目：CSS和JS的位置会影响页面效率，为什么？

先说CSS。CSS的位置不会影响加载速度，但是CSS一般放在`<head>`标签中。前面有说DOM树和CSS树共同生成渲染树，CSS位置太靠后的话，在CSS加载之前，可能会出现闪屏、样式混乱、白屏等情况。

再说JS。JS是阻塞加载，默认的`<script>`标签会加载并且立即执行脚本，如果脚本很复杂或者网络不好，会出现很久的白屏。所以，JS标签一般放到`<body>`标签最后。

现在，也可以为`<script>`标签设置`async`或者`defer`属性。前者是js脚本的加载和执行将与后续文档的加载和渲染同步执行。后者是js脚本的加载将与后续文档的加载和渲染同步执行，当所有元素解析完，再执行js脚本。

## 4. 算法相关

#### 4.1 数组全排列

> 题目：现在有一个数组\[1,2,3,4\]，请实现算法，得到这个数组的全排列的数组，如\[2,1,3,4\]，\[2,1,4,3\]。。。。你这个算法的时间复杂度是多少

实现思路：从“开始元素”起，每个元素都和开始元素进行交换；不断缩小范围，最后输出这种排列。暴力法的时间复杂度是 $O(N_N)$，递归实现的时间复杂度是 $O(N!)$

**如何去重？去重的全排列就是从第一个数字起每个数分别与它后面非重复出现的数字交换。**对于有重复元素的数组，例如：`[1, 2, 2]`，应该剔除重复的情况。每次只需要检查`arr[start, i)`中是不是有和`arr[i]`相同的元素，有的话，说明之前已经输出过了，不需要考虑。

代码实现：
```javascript
const swap = (arr, i, j) => {
  let tmp = arr[i]
  arr[i] = arr[j]
  arr[j] = tmp
}

const permutation = arr => {
  const _permutation = (arr, start) => {
    if(start === arr.length){
      console.log(arr)
      return
    }
    for(let i = start; i < arr.length; ++i){
      // 全排列：去重操作
      if(arr.slice(start, i).indexOf(arr[i]) !== -1){
        continue
      }
      swap(arr, i, start) // 和开始元素进行交换
      _permutation(arr, start + 1)
      swap(arr, i, start) // 恢复数组
    }
    return 
  }
  return _permutation(arr, 0)
}

permutation([1, 2, 2])
console.log("**********")
permutation([1, 2, 3, 4])
```


#### 4.2 背包问题

> 题目：我现在有一个背包，容量为m，然后有n个货物，重量分别为w1,w2,w3...wn，每个货物的价值是v1,v2,v3...vn，w和v没有任何关系，请求背包能装下的最大价值。

*这个还在学习中，背包问题博大精深。。。*

#### 4.3 图的连通分量

> 题目：我现在有一个canvas，上面随机布着一些黑块，请实现方法，计算canvas上有多少个黑块。

**这一题可以转化成图的联通分量问题**。通过`getImageData`获得像素数组，从头到尾遍历一遍，就可以判断每个像素是否是黑色。同时，准备一个`width * height`大小的二维数组，这个数组的每个元素是`1/0`。如果是黑色，二维数组对应元素就置1；否则置0。

然后问题就被转换成了图的连通分量问题。可以通过深度优先遍历或者并查集来实现。之前我用C++实现了，这里不再冗赘：
- [图的遍历和应用](https://godbmw.com/passages/2018-10-27-graph-traversal-application/#2.1%20%E6%89%BE%E5%88%B0%E5%9B%BE%E7%9A%84%E8%81%94%E9%80%9A%E5%88%86%E9%87%8F)
- [并查集](https://godbmw.com/passages/2018-10-11-union-find/)

## 5. Web工程化

#### 5.1 Dialog组件思路

> 题目：现在要你完成一个Dialog组件，说说你设计的思路？它应该有什么功能？

- 可以指定宽度、高度和位置
- 需要一个遮盖层，遮住底层内容
- 由头部、尾部和正文构成
- 需要监听事件和自定义事件，非单向数据流：例如点击组件右上角，修改父组件的`visible`属性，关闭组件。

关于工程化组件封装，可以去试试ElementUI。这个是ElementUI的Dialog组件：[Element-Dialog](http://element-cn.eleme.io/#/zh-CN/component/dialog)

#### 5.2 React的Diff算法和虚拟DOM

> 题目： react 的虚拟dom是怎么实现的

原答案写的挺好滴，这里直接贴了。

```
首先说说为什么要使用Virturl DOM，因为操作真实DOM的耗费的性能代价太高，所以react内部使用js实现了一套dom结构。
在每次操作在和真实dom之前，使用实现好的diff算法，对虚拟dom进行比较，递归找出有变化的dom节点，然后对其进行更新操作。
为了实现虚拟DOM，我们需要把每一种节点类型抽象成对象，每一种节点类型有自己的属性，也就是prop，每次进行diff的时候，react会先比较该节点类型：
假如节点类型不一样，那么react会直接删除该节点，然后直接创建新的节点插入到其中；
假如节点类型一样，那么会比较prop是否有更新，假如有prop不一样，那么react会判定该节点有更新，那么重渲染该节点，然后在对其子节点进行比较，一层一层往下，直到没有子节点。
```

参考链接：[React源码之Diff算法](https://segmentfault.com/a/1190000010686582)
