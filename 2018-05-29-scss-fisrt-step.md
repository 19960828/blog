---
title: 初识scss：配置与运行
date: 2018-05-29
categories:
- Scss
tags:
---

> `Sass` 和 `SCSS` 其实是同一种东西，我们平时都称之为 `Sass`。**他们都是用`Ruby`开发Css预处理器，`boostrap4`已经将`less`换成了`sass`。**

<!-- more -->

## 1、`SCSS`和`Sass`
> `Sass` 和 `SCSS` 其实是同一种东西，我们平时都称之为 `Sass`。**他们都是用`Ruby`开发Css预处理器，`boostrap4`已经将`less`换成了`sass`。**

不同之处：
- 文件拓展名：分别是`sass`和`scss`
- 缩进：`sass`严格缩进（类似python和ruby），`scss`是css的缩进样式
- 是否兼容css语法：显然，由于缩进的不同，`scss`是兼容原生的css写法。

总的来说，`scss`是`sass`升级版，兼容css语法，并且有着自己的独立语法。

## 2、环境配置

1. 安装ruby：windows注意添加注册表路径
2. 安装sass：利用ruby的包管理器`gem`安装，命令行运行:`gem install sass`
3. 升级和删除sass：`gem update/uninstall sass`

**如果国外源过慢？**

```shell
gem sources --remove https://rubygems.org/ 
gem sources -a https://ruby.taobao.org/
gem sources -l #查看是不是淘宝源
```

## 3、编译
> 编译指的是：将scss文件编译为css文件的过程。

### 3.1 源文件编译

**单文件编译**

```shell
# 格式：sass 待编译的Sass文件名:编译后CSS文件名
scss scss.scss:css.css
```

**实时自动编译**

使用`--watch`参数即可，scss会在源文件改动时候，自动重新编译。

```shell
scss --watch scss.scss:css.css # 方便
```

### 3.2 输出文件风格

> 命令行编译时候，使用`--style`参数。

一段scss代码：
```scss
body {
  h1 {
    color : red;
  }
}
```

**默认：嵌套输出方式 nested**
```scss
body h1 {
  color: red; }
```

**展开输出方式 expanded**
```scss
body h1 {
  color: red;
}
```

**紧凑输出方式 compact**
```scss
body h1 { color: red; }
```

**压缩输出方式 compressed**
```scss
body h1{color:red}
```

## 4. 注意

> 最新的scss开启了`sourcemap`功能，`--sourcemap`参数默认添加。
