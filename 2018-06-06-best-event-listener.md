---
title: 最优-scroll事件的监听实现
date: 2018-06-06
categories:
- JavaScript
tags:
- 算法
---

关于`scroll`等类似高频率事件的最优实现攻略。

你会怎么实现一个**监听HTML元素滚动到底部**这个看起来很简单的函数方法？

<!-- more -->

关于`scroll`等类似高频率事件的最优实现攻略。

你会怎么实现一个**监听HTML元素滚动到底部**这个看起来很简单的函数方法？

## 1. 背景和目标
前端在监听`scroll`这类高频率触发事件时，常常需要一个监听函数来实现监听和回调处理。传统写法上利用`setInterval`或`setTimeout`来实现。

**为了减小CPU开支，往往需要节流函数，但是，`interval`的指定依旧是个难题。`interval`较大，会处理不及时；较小，占用内存资源。**

> 为了实践和解决问题，打算实现一个**监听HTML元素滚动到底部的函数**：
> 1. 监听指定HTML元素的`scroll`事件，并**正确判断是否到底部**
> 2. 正确确定确定**回调间隔**
> 3. 正确使用**节流函数**
> 4. **组件封装**

## 2. `window.requestAnimationFrame()`
> 前文说到，如果利用`setTimeout`或者`setInterval`，回调间隔`interval`很难确定。最理想的情况就是：**回调间隔等于显示屏（浏览器）刷新频率。**

浏览器刷新频率一般会略低于显示屏刷新频率，为`16.7次/ms`。具体说，就是：**`scroll`事件每次触发时候的时间间隔**。通过代码来看一下：

```javascript
var app = document.getElementById('app')
app.addEventListener('scroll' , function() {
  console.log((new Date()).getTime())
})
```

控制台输出：
![1.png](/images/JavaScript/最优-scroll事件的监听实现/1.png)

可以看到，有时候间隔是10ms，有时候是30ms，如果我们自己来设定`interval`，应该取最小值。**然而，不同浏览器和不同电脑的刷新频率不确定**。如果设置过小，还会造成刷新频率低的显示屏的CPU损耗。

所以，使用`window.requestAnimationFrame()`来让浏览器根据刷新频率自动设置`interval`。我们只需要关注回调函数即可。

当然，这个函数本身还实现了很多优化，[可以点我看一下](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/requestAnimationFrame)。

## 3. 节流函数

由于`window.requestAnimationFrame()`的特效，所以它可以在同一帧中被重复绘制。**这时候，就需要节流函数，保证`requestAnimationFrame`的回调队列中只有一个函数在执行**。

```javascript
// 节流函数 : 减少浏览器内存消耗
function throttle(ele , callback) {
  var isRunning = false 
  return function() {
    if (isRunning) return
    isRunning = true
    // requestAnimationFrame:回调间隔 = 浏览器重绘频率
    window.requestAnimationFrame(function(timestamp) { 
      if(ele.scrollTop + ele.clientHeight >= ele.scrollHeight) { // 检测是否滚动到元素底部
        callback()
      }
      isRunning = false
    })
  }
}
```

## 4. 代码封装
> 函数封装详见[script.js](https://github.com/dongyuanxin/markdown-static/blob/master/JavaScript/%E6%9C%80%E4%BC%98-scroll%E4%BA%8B%E4%BB%B6%E7%9A%84%E7%9B%91%E5%90%AC%E5%AE%9E%E7%8E%B0/script.js)，调用样例详见[index.html](https://github.com/dongyuanxin/markdown-static/blob/master/JavaScript/%E6%9C%80%E4%BC%98-scroll%E4%BA%8B%E4%BB%B6%E7%9A%84%E7%9B%91%E5%90%AC%E5%AE%9E%E7%8E%B0/index.html)

基于上面，我们封装`script.js`：
```javascript
// 节流函数 : 减少浏览器内存消耗
function throttle(ele , callback) {
  var isRunning = false 
  return function() {
    if (isRunning) return
    isRunning = true
    // requestAnimationFrame:回调间隔 = 浏览器重绘频率
    window.requestAnimationFrame(function(timestamp) { 
      if(ele.scrollTop + ele.clientHeight >= ele.scrollHeight) { // 检测是否滚动到元素底部
        callback()
      }
      isRunning = false
    })
  }
}

/**
 * 监听HTML元素是否滚动到底部 : 兼容ES5
 * @param {object} ele HTML元素
 * @param {function} callback 滚动到底部后的回调函数
 */
function listenScrollToBottom(ele , callback) {
  if(ele === null || ele === undefined) { // 节点不存在：抛出错误
    throw new Error('Undefined COM')
    return 
  }
  ele.addEventListener("scroll" , throttle(ele , callback) , false) // 监听 scroll 事件
}
```

将需要监听的HTML元素和回调函数传入，即可在HTML元素滚动到底部时，触发相应的操作。例如：瀑布流、缓冲加载等。下面是控制台输出一段文字。

```html
<body>
  <div id="app">
    <div class="inner"></div>
  </div>
  <script>
    var app = document.getElementById('app')
    listenScrollToBottom(app , function() { // 回调函数
      console.log("Scroll to bottom")
    })
  </script>
</body>
```