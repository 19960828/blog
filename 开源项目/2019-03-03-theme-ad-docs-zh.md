---
title: "Theme Art Design 中文文档"
date: 2019-03-03
categories: 开源项目
tags:
- UI设计
- Hexo
permalink: "2019-03-03-theme-ad-docs-zh"
---

> 伴随着Theme AD Version 1.0.0的正式发布，文档热气出炉，敬请食用！

周天写了一下午 + 一晚上，是按照`theme next`文档的模板写的。如有错误或建议，欢迎指正和提出。

顺便吐槽下，写文档真的好累，也希望大家用的顺手！

<!-- more -->

## 0. 一些说明

文档较长，如果您是一位hexo老玩家或者使用过`theme-bmw`，那么可以考虑直接打开右侧目录，阅读所需要的章节。

把联系方式放在开头，真害怕大家没耐心看下去（有很多好玩的功能，别错过哦）。

- Email：yuanxin.me@gmail.com
- QQ群：534018786
- 网站留言

## 1. 安装Art Design

### 下载主题

在终端窗口下，定位到 Hexo 站点目录下。使用 `Git` checkout 代码：

```ba
cd your-blog
git clone https://github.com/dongyuanxin/theme-ad.git themes/ad
```

### 启用主题

当 克隆/下载 完成后，打开**站点配置文件**， 需要完成下面2件事情：

1. **必做**：找到 `theme` 字段，并将其值更改为 `ad`。
2. **推荐**：找到`permalink`字段，并将其更改为`passages/:title/`。

```yml
# 位置：大概位于 18 行
permalink: passages/:title/ # 如果您需要开启评论和文章统计，请修改此配置

# 位置：大概位于 76 行
theme: ad # 启用 "Art Design" 主题
```

现在，运行`hexo s`，来看看主题的样子吧！

## 2. 文章发布

### 文件名称

为了更好的标识文章和防止重复，文章对应的markdown文件的命名应该遵循：`YYYY-MM-DD-title` 格式。

例如，今天是2019年3月3日，文章内容是与ES6有关。那么，文件名是`2019-03-03-es6.md`。

### Front-matter

主题通过文章Front-matter中的信息，自动生成对应页面和渲染UI。

下面，是一个符合要求的Front-matter：

```yml
---
title: "文章标题"
date: 2018-11-28
categories: 文章分类 # 分类只能有1个
tags: # 标签可以有多个
- 标签1
- 标签2
- 更多标签
cover: "https://text.com/demo.png" # 文章封面图片URL
---
```

### 摘要与正文

`AD`主题推荐分开书写文章的摘要与正文，展示页面渲染的是文章摘要部分，文章页面渲染的是文章正文部分。

**方法1**：推荐使用`<!-- more -->`，除了可以精确控制需要显示的摘录内容以外， 这种方式也可以让 Hexo 中的插件更好的识别。

```yml
文章摘要写在前面，支持markdown左右语法。

<!-- more -->

文章正文写在这里。
```

**方法2**：在Front-matter中设置`description`字段。这种方式会覆盖方法1的摘要，并且只支持原生文本。

```yml
---
title: "文章标题"
date: 2018-11-28
description: "文章摘要... ..."
categories: 
tags: 
---
```

### 密码拦截

在数据隐私愈发重要的今天，主题为文章提供了**密码拦截**功能。

主题使用的是`sha256`不可逆加密算法，例如密码是`godbmw`，那么放在配置文件中的密码字段应该是：`efe07af7441da2b69c4a41e42e73be4db47f66010a56900788a458354a7373ec`

**方法1**：在主题配置文件中修改`passwords`字段，例如：

```yml
passwords: 
  - efe07af7441da2b69c4a41e42e73be4db47f66010a56900788a458354a7373ec 
```

**方法2**：在指定文章的Front-matter中设置`passwords`字段，例如：

```yml
---
title: "文章标题"
date: 2018-11-28
categories: 
tags: 
passwords: 
  - efe07af7441da2b69c4a41e42e73be4db47f66010a56900788a458354a7373ec
---
```

**注意**：

1. 方法1添加的`passwords`具有全部加锁文章的阅读权限。可以用来保存只有自己知道的密码，方便阅读。
2. 方法2添加的`passwords`只有对应文章的阅读权限。可以为专门向特定的人开启对应的权限，比如他 / 她。
3. 密码具有72小时的有效期，一旦超过了72小时，下次阅读需要重新输入。
4. **仅具有拦截功能和警示作用，并不是完全安全**。

## 3. 主题配置

**注意**：请在**主题配置文件**中修改如下配置。并且配置信息的优先级是：主题配置文件 > 站点配置文件 > 主题默认配置信息。

### 设置导航栏

更改`nav_name`和`motto`字段，修改导航栏左侧文字Logo和子标题 / 座右铭，例如：

```yml
nav_name: GODBMW # 导航栏头部名称

motto: "安静写些东西" # 导航栏座右铭
```

更改`nav`字段，修改导航栏的快速跳转链接。

**注意**：如果没有详细阅读使用文档，请不要修改此字段。

### 开启「标签云」页面

“标签云”页面展示站点的所有标签，主题以为您设计好了相关UI。

1. 创建标签页：`hexo new page tags`

2. 配置标签页：修改标签页对应的md文件(`your-blog/source/tags/index.md`)的Front-matter，为其添加`type`属性

   ```yml
   ---
   title: tags
   date: 2019-02-15 14:51:22
   type: "tags" # 必须显式设置为"tags"
   ---
   ```

### 开启「分类」页面

“分类”页面展示站点的所有分类，主题以为您设计好了相关UI。

1. 创建分类页：`hexo new page categories`

2. 配置分类页：修改分类页对应的md文件(`your-blog/source/categories/index.md`)的Front-matter，为其添加`type`属性

   ```yml
   ---
   title: categories
   date: 2019-02-15 14:51:22
   type: "categories" # 必须显式设置为"categories"
   ---
   ```

### 开启「友链」页面

友链界面是专门为友链设计，采用卡片式的UI。

1. 创建友链页：`hexo new page friends`

2. 配置友链页：修改友链页对应的md文件(`your-blog/source/friends/index.md`)的Front-matter，为其添加`type`属性

   ```yml
   ---
   title: friends
   date: 2019-02-15 21:52:40
   type: "friends" # 必须显式设置为"friends"
   ---
   ```

3. 编写友链页面内容：直接在友链页对应的md文件(`your-blog/source/friends/index.md`)编写内容，相关内容会被自动渲染到友链页面。推荐放置友链的招入信息和要求；当然，您也可以不编写任何内容。

### 开启「介绍」页面

介绍页面是专门为个人展示设计，可以放置站点信息、个人简历等等。

1. 创建介绍页：`hexo new page about`

2. 配置介绍页：修改介绍页对应的md文件(`your-blog/source/about/index.md`)的Front-matter，为其添加`type`属性

   ```yml
   ---
   title: about
   date: 2019-02-15 21:34:33
   type: "about" # 必须显式设置为"about"
   ---
   ```

3. 编写介绍页：直接在介绍野对应的md文件(`your-blog/source/about/index.md`)编写内容，相关内容会被自动渲染到介绍页面。

### 设置「代码高亮主题」

`AD` 使用 [Tomorrow Theme](https://github.com/chriskempson/tomorrow-theme) 作为代码高亮，共有5款主题供你选择。 `AD`默认使用的是 白色的 `night eighties` 主题，可选的值有 `normal`，`night`， `night blue`， `night bright`， `night eighties`

更改`highlight_theme`字段，选择对应的主题，例如：

```yml
highlight_theme: night eighties
```

### 设置「数学公式渲染」

为了方便数学、深度学习、物理工程等领域的文章的展示和书写，主题提供了对`Latex`数学公式的全面支持。

更改`mathjax`字段，开启数学公式渲染：

```yml
mathjax: true
```

### 设置友情链接

更改`friends`字段，添加友链信息，例如：

```yml
friends:
  -
    nickname: GodBMW # 友链名称
    avatar: /images/friends/godbmw.jpeg # 友链头像
    site: https://godbmw.com/ # 友链地址
    meta: 主题作者 # 友链介绍
    show: true # 是否展示此友链
```

如果您的友链头像都保存在其他服务器上，可以通过更改`friends_avatar_cdn`字段来配置前缀地址，例如：

```yml
friends_avatar_cdn: "https://godbmw.com/blog-static"
```

**注意**：此功能需要正确开启「友链」页面。

### 开启侧边栏分享链接

侧边栏分享支持twitter、facebook、weibo、qq和wechat总共5个常用社交平台的快速分享，可以借助它们让浏览者快速分享**任何**页面。

更改`share`字段，选择社交平台，例如：

```yml
share:
  twitter: true # true 是开启
  facebook: false # false 是关闭
  weibo: true
  qq: true
  wechat: true
```

### 开启打赏功能

付费阅读时代的到来，特意增加了以“二维码”为支付方式的打赏功能，可以添加不限数量的支付平台的二维码信息。

更改`reward`字段，添加打赏二维码，例如：

```yml
reward:
  -
    src: /images/wechat.png # 二维码图片地址
    alt: "WeChat" # 二维码信息
  -
    src: /images/alipay.png
    alt: "AliPay"
```

### 开启版权保护

主题全局监听了浏览者在网站上的所有复制操作，并且自动为内容追加了网站的版权信息。

但是对于**搜索引擎**等自动爬取网站的机器人，需要在文章底部添加版权信息，以达到声明版权的目的。

更改`copyright`字段，开启版权保护，例如：

```yml
copyright:
  enable: true # 是否开启
  author: "董沅鑫" # 作者信息
  show_link: true # 是否展示文章链接
  more: '版权声明: 本博客所有文章除特别声明外, 均采用CC BY-NC-SA 4.0许可协议. 转载请注明出处!' # 自定义字段，支持HTML渲染
```

### Github快速通道

主题在页面右上角提供了Github快速跳转的按钮。可以放置个人github地址、博客文章仓库地址等等。

更改`github`字段，例如：

```yml
github: "https://github.com/dongyuanxin/"
```

## 4. 进阶设定

**注意**：除了“配置静态图片CDN”修改**`package.json`**，其他均是修改**主题配置文件**。

### Leancloud

主题的评论功能、PV统计和提醒功能均是依托于`Leancloud`平台，借助`ServerLess`实现了无后台服务和数据统一管理。

更改`leancloud`字段，例如：

```yml
leancloud:
  appid: Hyq9wkH495DgNHWhDQCOfQSp-gzGzoHsz # 填写您的appid
  appkey: WaR7nrzhliHj9aVwdQzkdlGd # 填写您的appkey
```

**注意**：请用自己账户应用的`appid`和`appkey`替换主题配置文件中的示例。

### 评论功能

评论数据均在您的`Leancloud`账号对应应用的`Comment`表中。

更改`leancloud.comment`字段，例如：

```yml
leancloud:
  appid: Hyq9wkH495DgNHWhDQCOfQSp-gzGzoHsz # 填写您的appid
  appkey: WaR7nrzhliHj9aVwdQzkdlGd # 填写您的appkey
  comment: true # 开启评论功能
```

### PV统计

PV数据均在您的`Leancloud`账号对应应用的`Counter`表中。同时，PV数据会在页脚进行展示。

更改`leancloud.count`字段，例如：

```yml
leancloud:
  appid: Hyq9wkH495DgNHWhDQCOfQSp-gzGzoHsz # 填写您的appid
  appkey: WaR7nrzhliHj9aVwdQzkdlGd # 填写您的appkey
  count: true # 是否开启文章统计
```

### 访问提醒功能

开启此功能会在浏览者第一次进入网站时进行提醒；如果浏览者超过一定时间（默认30天）没有登陆网站，主题也会自动提醒。

更改`leancloud.welcome`字段，例如：

```yml
welcome: 
  enable: true # 是否开启提醒功能
  interval: 30 # 多久不浏览会再次提醒，单位：天
```

**注意**：提醒功能需要开启`Leancloud`。

### SEO优化

为了让网站更容易被搜索引擎收录，获得更高SEO，主题提供了百度、谷歌和360这3种搜索引擎的自动推送服务。

更改`analytics`字段，例如：

```yml
analytics:
  baidu:
    enable: true # 开启对Baidu的自动推送
  google:
    enable: true # 开启对Google的自动推送
    id: # Google跟踪代码的id
  "360":
    enable: true # 开启对360的自动推送
    id: # 360跟踪代码的id
```

**注意**：360和谷歌的自动推送，需要对应平台的跟踪代码的id，请查阅对应平台的手册内容。

### 配置静态图片CDN

为了更方便地管理文章和静态图片资源，主题提供了配置静态资源CDN。

更改主题文件夹下的`package.json`文件的`imgcdn`字段，例如：

```json
{
  "imgcdn": "https://godbmw.com/blog-static"
}
```

文章中如果使用了图片资源，例如 `![](/images/test.png)`。都会被替换为：`https://godbmw.com/blog-static/images/test.png`。

借助此功能，使用者可以将静态图片资源部署在CDN或者其他服务器上，文章仓库仅仅存放文章对应的`Markdown`。避免随着文章数量的增加，静态图片资源造成的仓库大小不断膨胀。

### 底部导航栏

底部导航栏采用多列多行的“栅栏式”布局，可以放置“推荐博客”、“开源项目”、“特别支持”等常用但不属于网站主体的内容。

更改`footer`字段，例如：

```yml
footer:
  - 
    title: "博客推荐"
    children:
      - 
        name: "GodBMW"
        path: "https://godbmw.com/"
      - 
        name: "阮一峰的个人网站"
        path: "http://ruanyifeng.com/"
  -
    title: "抓到我"
    children: 
      -
        name: "掘金"
        path: "https://juejin.im/user/5b91fcf06fb9a05d3c7fd4a5"
      -
        name: "知乎"
        path: "https://www.zhihu.com/people/godbmw/activities"
```

### 底部其他信息

更改`footer_contact`字段，填写自己的联系方式，例如：

```yml
Email: yuanxin.me@gmail.com
```

更改`start_time`字段，填写建站时间，例如：

```yml
start_time: "2018-02-10"
```

### 自定义脚本与样式文件

更改`custom_styles`字段，引入自定义样式文件，例如：

```yml
custom_styles: 
  - style.css
  - style2.css
```

更改`custom_scripts`字段，引入自定义脚本文件，例如：

```yml
custom_scripts: 
  - script.js
  - script2.js
```

**注意**：为了不阻塞页面，自定义脚本文件被插入到`</body>`前，而不是`<head>`中。

## 5. 警告

### 尊重原创

1. 您可以根据个人需要修改页面底部的说明信息，但请不要去除`theme-ad`主题的版权声明 
2. 评论系统采用了`Valine`，请不要去除Valine的版权声明 
3. 尊重原创，也祝您在开源社区玩得开心

### Web安全

如果您开启了`Leancloud`平台，以及相关评论功能、PV功能或者访问提醒功能，请仔细阅读此节！

借助了`Leancloud`规避了后端部署，傻瓜式一键启动相关功能。但随之而来的是，暴露在浏览器环境下的`appid`和`appkey`带来的安全问题。

请进入`leancloud`中您的应用 => 左侧导航栏 => 设置 => 安全中心，进行相关配置：

![](/images/开源项目/Theme-BMW中文文档/8.png)

首先，关闭不需要的“服务开关” (仅保留“数据存储”服务)：

![](/images/开源项目/Theme-BMW中文文档/9.png)

最后，设置您的“Web”安全域名 (博客/个人网站地址):

![](/images/开源项目/Theme-BMW中文文档/10.png)

