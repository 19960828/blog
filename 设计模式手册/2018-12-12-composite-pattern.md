---
title: "每天一个设计模式之组合模式"
date: "2018-12-12"
categories: 设计模式手册
tags:
  - 设计模式
  - Python
  - ES6
permalink: "2018-12-12-composite-pattern"
---

> 组合模式，将对象组合成树形结构以表示“部分-整体”的层次结构。

1. 用小的子对象构造更大的父对象，而这些子对象也由更小的子对象构成
2. **单个对象和组合对象对于用户暴露的接口具有一致性**，而同种接口不同表现形式亦体现了多态性

<!-- more -->

> 作者按：《每天一个设计模式》旨在初步领会设计模式的精髓，目前采用`javascript`和`python`两种语言实现。诚然，每种设计模式都有多种实现方式，但此小册只记录最直截了当的实现方式 :)

## 0. 项目地址

- [组合模式·全部代码](https://github.com/dongyuanxin/design-pattern-demos/tree/master/composite_pattern)
- [《每天一个设计模式》地址](https://godbmw.com/categories/%E6%AF%8F%E5%A4%A9%E4%B8%80%E4%B8%AA%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F)

## 1. 什么是“组合模式”？

> 组合模式，将对象组合成树形结构以表示“部分-整体”的层次结构。

1. 用小的子对象构造更大的父对象，而这些子对象也由更小的子对象构成
2. **单个对象和组合对象对于用户暴露的接口具有一致性**，而同种接口不同表现形式亦体现了多态性

## 2. 应用场景

组合模式可以在需要针对“树形结构”进行操作的应用中使用，例如扫描文件夹、渲染网站导航结构等等。

## 3. 代码实现

这里用代码**模拟文件扫描功能**，封装了`File`和`Folder`两个类。在组合模式下，用户可以向`Folder`类嵌套`File`或者`Folder`来模拟真实的“文件目录”的树结构。

同时，两个类都对外提供了`scan`接口，`File`下的`scan`是扫描文件，`Folder`下的`scan`是调用子文件夹和子文件的`scan`方法。整个过程采用的是**深度优先**。

### 3.1 python3 实现

```python
class File:  # 文件类
    def __init__(self, name):
        self.name = name

    def add(self):
        raise NotImplementedError()

    def scan(self):
        print('扫描文件：' + self.name)


class Folder:  # 文件夹类
    def __init__(self, name):
        self.name = name
        self.files = []

    def add(self, file):
        self.files.append(file)

    def scan(self):
        print('扫描文件夹: ' + self.name)
        for item in self.files:
            item.scan()


if __name__ == '__main__':

    home = Folder("用户根目录")

    folder1 = Folder("第一个文件夹")
    folder2 = Folder("第二个文件夹")

    file1 = File("1号文件")
    file2 = File("2号文件")
    file3 = File("3号文件")

    # 将文件添加到对应文件夹中
    folder1.add(file1)

    folder2.add(file2)
    folder2.add(file3)

    # 将文件夹添加到更高级的目录文件夹中
    home.add(folder1)
    home.add(folder2)

    # 扫描目录文件夹
    home.scan()

```

执行`$ python main.py`, 最终输出结果是：

```
扫描文件夹: 用户根目录
扫描文件夹: 第一个文件夹
扫描文件：1号文件
扫描文件夹: 第二个文件夹
扫描文件：2号文件
扫描文件：3号文件
```

### 3.2 ES6 实现

```javascript
// 文件类
class File {
  constructor(name) {
    this.name = name || "File";
  }

  add() {
    throw new Error("文件夹下面不能添加文件");
  }

  scan() {
    console.log("扫描文件: " + this.name);
  }
}

// 文件夹类
class Folder {
  constructor(name) {
    this.name = name || "Folder";
    this.files = [];
  }

  add(file) {
    this.files.push(file);
  }

  scan() {
    console.log("扫描文件夹: " + this.name);
    for (let file of this.files) {
      file.scan();
    }
  }
}

let home = new Folder("用户根目录");

let folder1 = new Folder("第一个文件夹"),
  folder2 = new Folder("第二个文件夹");

let file1 = new File("1号文件"),
  file2 = new File("2号文件"),
  file3 = new File("3号文件");

// 将文件添加到对应文件夹中
folder1.add(file1);

folder2.add(file2);
folder2.add(file3);

// 将文件夹添加到更高级的目录文件夹中
home.add(folder1);
home.add(folder2);

// 扫描目录文件夹
home.scan();
```

执行`$ node main.js`，最终输出结果是：

```
扫描文件夹: 用户根目录
扫描文件夹: 第一个文件夹
扫描文件: 1号文件
扫描文件夹: 第二个文件夹
扫描文件: 2号文件
扫描文件: 3号文件
```

## 4. 参考

- 《JavaScript 设计模式和开发实践》
