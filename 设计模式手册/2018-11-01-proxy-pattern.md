---
title: 设计模式手册之代理模式
date: 2018-11-01
categories: 设计模式手册
tags:
- Python
- 设计模式
- JavaScript
- 前端优化
permalink: "2018-11-01-proxy-pattern"
---

> 代理模式的定义：为一个对象提供一种代理以方便对它的访问。

**代理模式可以解决避免对一些对象的直接访问**，以此为基础，常见的有保护代理和虚拟代理。保护代理可以在代理中直接拒绝对对象的访问；虚拟代理可以延迟访问到真正需要的时候，以节省程序开销。

<!-- more -->

## 1. 什么是代理模式？

> 代理模式的定义：为一个对象提供一种代理以方便对它的访问。

**代理模式可以解决避免对一些对象的直接访问**，以此为基础，常见的有保护代理和虚拟代理。保护代理可以在代理中直接拒绝对对象的访问；虚拟代理可以延迟访问到真正需要的时候，以节省程序开销。

## 2. 代理模式优缺点

代理模式有高度解耦、对象保护、易修改等优点。

同样地，因为是通过“代理”访问对象，因此开销会更大，时间也会更慢。

## 3. 代码实现

### 3.1 python3 实现

```python
class Image:
  def __init__(self, filename):
    self.filename = filename

  def load_img(self):
    print("finish load " + self.filename)

  def display(self):
    print("display " + self.filename)

# 借助继承来实现代理模式
class ImageProxy(Image):
  def __init__(self, filename):
    super().__init__(filename)
    self.loaded = False

  def load_img(self):
    if self.loaded == False:
      super().load_img()
    self.loaded = True

  def display(self):
    return super().display()


if __name__ == "__main__":
  proxyImg = ImageProxy("./js/image.png")

  # 只加载一次，其它均被代理拦截
  # 达到节省资源的目的
  for i in range(0,10):
    proxyImg.load_img()

  proxyImg.display()
```

### 3.2 javascript 实现

**`main.js` :**

```javascript
// main.js
const myImg = {
  setSrc(imgNode, src) {
    imgNode.src = src;
  }
};

// 利用代理模式实现图片懒加载
const proxyImg = {
  setSrc(imgNode, src) {
    myImg.setSrc(imgNode, "./image.png"); // NO1. 加载占位图片并且将图片放入<img>元素

    let img = new Image();
    img.onload = () => {
      myImg.setSrc(imgNode, src); // NO3. 完成加载后, 更新 <img> 元素中的图片
    };
    img.src = src; // NO2. 加载真正需要的图片
  }
};

let imgNode = document.createElement("img"),
  imgSrc =
    "https://upload-images.jianshu.io/upload_images/5486602-5cab95ba00b272bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp";

document.body.appendChild(imgNode);

proxyImg.setSrc(imgNode, imgSrc);
```

**`main.html` :**

```html
<!-- main.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>每天一个设计模式 · 代理模式</title>
</head>
<body>
  <script src="./main.js"></script>
</body>
</html>
```

## 4. 参考

- [代理模式](https://www.runoob.com/design-pattern/proxy-pattern.html)
- 《JavaScript 设计模式和开发实践》
