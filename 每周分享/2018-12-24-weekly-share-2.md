---
title: "每周分享第 2 期"
date: "2018-12-24"
categories:
  - 每周分享
tags: 
  - JavaScript
  - Git
  - GitHub
  - CSS3
  - ReactJS
  - Golang
permalink: "2018-12-24-weekly-share-2"
---

> 每每周分享会系统梳理过去一周我看的的值得记录和分享的技术、工具、文章/段子，致力于为收藏夹“瘦身”！

👇 内容速览 👇

- 谁说虚拟 DOM 比原生 DOM 操作快？（尤大大亲答）
- Windows 平台下使用 Git 出现“warning: LF will be replaced by CRLF”？
- 怎么清空一个 github 线上仓库？
- 为什么要用`Golang`替代`Python`?
- `React`封装`Toast`组件
- ...

<!-- more -->

> 每周分享会系统梳理过去一周我看的的值得记录和分享的技术、工具、文章/段子，致力于为收藏夹“瘦身”！

👇 内容速览 👇

- 谁说虚拟 DOM 比原生 DOM 操作快？（尤大大亲答）
- Windows 平台下使用 Git 出现“warning: LF will be replaced by CRLF”？
- 怎么清空一个 github 线上仓库？
- 为什么要用`Golang`替代`Python`?
- `React`封装`Toast`组件
- ...

## 技术

1、[谁说虚拟 DOM 比原生 DOM 操作快？（尤大大亲答）](https://www.zhihu.com/question/31809713/answer/53544875)

这是 VueJs 作者尤雨溪亲自回答的问题。毫无疑问，大家对虚拟 DOM 存在着误解，**React 官方从来没有说虚拟 DOM 比原生操作更快**。其实，框架封装的意义在于对掩盖底层 DOM，提供高可维护性的 API，在这之上保证过得去的性能。

2、[Windows 平台下使用 Git 出现“warning: LF will be replaced by CRLF”？](https://stackoverflow.com/questions/5834014/lf-will-be-replaced-by-crlf-in-git-what-is-that-and-is-it-important)

CRLF 是 windows 下的文件换行标志：回车 + 换行符；LF 是 Mac/Linux 的换行标志：换行符。如果只是在 Mac/Linux 下进行开发，不要担心这个。如果涉及 Windows 的开发，git 默认是`git config core.autocrlf true`：**检出文件到编辑器的时候会将`LF`转化为`CRLF`，写入 git 记录时候统一使用`LF`**。而`git config cor.autocrlf input`保留原来的换行符，这种做法并不推荐。

3、[怎么清空一个 github 线上仓库？](https://stackoverflow.com/questions/28578581/how-to-completely-clear-git-repository-without-deleting-it)

之前在做开源博客框架的时候，一次失误操作把`github`上的仓库环境搞乱了。当时不能`delete`仓库（因为有很多 Star），**怎么才能清空仓库，重新上传正确的代码**？由于`github`不提供清空的快捷键，需要手动操作，思路如下：

```sh
cd your-repo # 进入本地正确的仓库目录
git remote remove origin # 移除原来的origin
git remote add origin remote-repo-url # 添加要清空的仓库的 git/https 地址
git push -f origin master # 将本地正确的提交记录强制提交上去
```

4、[CSS3 的`appearance`属性](https://www.w3cplus.com/css3/changing-appearance-of-element-with-css3.html)

**`appearance`可以被用来改变任何元素的浏览器默认风格**。比如说不喜欢一些浏览器的`button`样式，如果要覆盖需要修改较多默认属性；现在可以直接通过`appearance: none;`来实现。

但请不要混淆`appearance`和 html5 语义化，比如把一个`div`元素设置成`appearance: button`。

5、[你不知道的 Chrome 调试工具技巧](https://juejin.im/post/5c09a80151882521c81168a2)

原作者是 [Tomek Sułkowski](https://link.juejin.im/?target=https%3A%2F%2Ftwitter.com%2Fsulco) 发布在 medium 上的一个系列。这是国内的翻译文章，发布在掘金社区。

6、[`React`封装`Toast`组件](https://juejin.im/post/5b63fdd46fb9a04fa7757081)

由于工作上的原因，开发技术栈从`Vue`全面转向了`React`，公司内部也有自己封装的 UI 组件。在实践的过程中，可以参考这篇文章学习一下如何封装一个组件，如何写动画，如何对外暴露接口等等。

当然，熟悉`ts`的话可以直接去翻`antd`的源码。

7、[如何验证`React`中嵌套对象的`PropTypes`](https://codeday.me/bug/20170619/28958.html)

经验性知识点，借助`prop-types`的`shape`函数即可。

```javascript
import React, { Component } from "react";
import PropTypes from "prop-types";

class Demo extends Component {
  // this.props.data 嵌套对象
  static propTypes = {
    data: PropTypes.shape({
      id: PropTypes.number.isRequired,
      title: PropTypes.string
    })
  };

  // ...
}
```

8、[为什么要用`Golang`替代`Python`?](https://www.zhihu.com/question/291435860)

曾经我也是一个忠实的`Pythoner`，后来随着项目经历的增加，渐渐发现动态类型语言容易写烂项目，而且语言本身的性能瓶颈真的难以突破！

很多人会讨论 Python 的性能以及优化，但是场景好像都不是很有分量，直到知乎宣布后端用 Go 重写-[相关链接](https://zhuanlan.zhihu.com/p/48039838)，终于一锤定音，Go 语言魅力可见一斑。有兴趣的朋友可以学习一下。

## 工具

1、[CODELF](https://unbug.github.io/codelf/)

![codelf](/images/每周分享/002/codelf.png)

一款国人做的变量命名网站，支持中文（通过调用有道免费的翻译 API）强迫症患者利器，但还是学好英语才是重点。

2、[蓝湖](https://lanhuapp.com/?home)

![lanhuapp](/images/每周分享/002/lanhuapp.png)

团队产品开发工具，降低沟通成本，支持 PSD 格式。

## 网站

1、[即刻](https://www.ruguoapp.com/)

年轻人的兴趣社区，是“兴趣社区”的深度尝试。大胆抛弃了“今日头条”强行信息倒流的做法，更提倡定制化和个性化。

社区氛围很好，无所谓大佬弱鸡，大家平等交流！

_特别感谢下 [https://chungzh.cn/](https://chungzh.cn/) 的推荐_

## 见识

1、[身患孤独症的周星驰：我让全世界笑过，但岁月却没有饶过我！](https://www.jianshu.com/p/f984436ae228?utm_campaign=maleskine&utm_content=note&utm_medium=pc_all_hots&utm_source=recommendation)

星爷演的最好那出戏是在 7 岁那年：“虽然我演戏无数，但是我要说，我最好的戏，是在七岁那年。”

2. [你见过最渣的渣女有多渣？](https://www.zhihu.com/question/293207596/answer/550222589?utm_source=wechat_session&utm_medium=social&utm_oi=833609455009660928)

## 投稿

欢迎投稿，或推荐好玩的东西，方式是向 yuanxin.me@gmail.com 发邮件或者在每周分享文章的评论区留言。
