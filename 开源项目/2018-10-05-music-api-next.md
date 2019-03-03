---
title: "music-api-next：一款支持网易、虾米和QQ音乐的JS爬虫库"
date: 2018-10-05
categories:
- 开源项目
tags:
- 爬虫
- 第三方库
- npm
permalink: "2018-10-05-music-api-next"
---

> 如果你苦于挑选一个全方位、多平台、简便易用的音乐爬虫库，`music-api-next`是不二选择。

特性：
- 支持网易、虾米和QQ三大主流音乐平台
- 支持音乐关键词搜索
- 支持音乐链接下载
- 支持音乐评论爬取
- 支持回调和`async/await`写法
- 支持`webpack`打包部署
- 支持`pm2`服务器部署
- 可用、高效、稳定

<!-- more -->

## 音乐，无界

> [**让音乐无界**](https://godbmw.com/passage/64)

如果你苦于挑选一个全方位、多平台、简便易用的音乐爬虫库，`music-api-next`是不二选择。

特性：
- 支持网易、虾米和QQ三大主流音乐平台
- 支持音乐关键词搜索
- 支持音乐链接下载
- 支持音乐评论爬取
- 支持回调和`async/await`写法
- 支持`webpack`打包部署
- 支持`pm2`服务器部署
- 可用、高效、稳定

## 项目地址

- Github: [https://github.com/dongyuanxin/music-api-next](https://github.com/dongyuanxin/music-api-next)
- npm: [https://www.npmjs.com/package/music-api-next](https://www.npmjs.com/package/music-api-next)
- Document: [https://godbmw.com/passage/62](https://godbmw.com/passage/62)
- 中文文档：[https://godbmw.com/passage/63](https://godbmw.com/passage/63)

## 快速开始

```javascript
const musicAPI = require("music-api-next");

// 搜索接口: 返回指定关键词的搜索信息
musicAPI
  .searchSong({
    key: "周杰伦",
    page: 1,
    limit: 10,
    vendor: "qq"
  })
  .then(songs => console.log(songs))
  .catch(error => console.log(error.message));

// 歌曲信息接口: 返回指定歌曲的信息
musicAPI
  .getSong({
    id: "003OUlho2HcRHC",
    vendor: "qq"
  })
  .then(meta => console.log(meta))
  .catch(error => console.log(error.message));

// 评论接口: 返回指定歌曲的评论
musicAPI
  .getComment({
    id: "003OUlho2HcRHC",
    page: 1,
    limit: 20,
    vendor: "qq"
  })
  .then(comments => console.log(comments))
  .catch(error => console.log(error.message));
```
