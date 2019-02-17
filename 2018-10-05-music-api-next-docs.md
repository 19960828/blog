---
title: "music-api-next DOCS"
date: 2018-10-05
categories:
- 开源项目
tags:
- 爬虫
- 第三方库
- npm
---

> The English document of `music-api-next`: music API for search results, songs, comments from QQ, Xiami and Netease.

If you have any question, welcome to comment here.

<!-- more -->

> Music API for search results, songs, comments from QQ, Xiami and Netease.

For more information on how to get started and how to use `music-api-next`, please see [author's blog](https://godbmw.com/) and comment there. For source files and issues, please visit [the github repo](https://github.com/dongyuanxin/music-api-next).

[**DOCS**](https://godbmw.com/passage/62)

[**中文文档**](https://godbmw.com/passage/63)

## Install

```
npm install music-api-next --save
```

If you are in China, please use:

```
cnpm install music-api-next --save
```

## Quick Start

```javascript
const musicAPI = require("music-api-next");

// Search API: search keywords on qq, xiami or netease
musicAPI
  .searchSong({
    key: "周杰伦",
    page: 1,
    limit: 10,
    vendor: "qq"
  })
  .then(songs => console.log(songs))
  .catch(error => console.log(error.message));

// Song API: get music meta including URL
musicAPI
  .getSong({
    id: "003OUlho2HcRHC",
    vendor: "qq"
  })
  .then(meta => console.log(meta))
  .catch(error => console.log(error.message));

// Comment API: get comments for the specified song
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

## Run with a server

```shell
git clone git@github.com:dongyuanxin/music-api-next.git
cd music-api-next
npm install
// run server on port: 5050
node server.js
```

You can see the results of music APIs by accessing the url.

For example:

- Search API: `http://localhost:5050/search/song?key=周杰伦&page=1&limit=10&vendor=qq`
- Song API: `http://localhost:5050/get/song?id=003OUlho2HcRHC&vendor=qq`
- Comment API: `http://localhost:5050/get/comment?id=003OUlho2HcRHC&page=1&limit=10&vendor=qq`

## Run with webpack

First, package with webpack.

```shell
git clone git@github.com:dongyuanxin/music-api-next.git
cd music-api-next
npm install
// use webpack to package program
// pacakged file named 'music-api-next.js' is placed in ./dist/
webpack
```

Then, you can move `music-api-next.js` very conveniently and use it in the following ways:

```javascript
const musicAPI = require("./music-api-next");

// ...
```

## API

- `musicAPI.searchSong(query)`:

  ```
  query: {
    key: String,
    page: Number,
    limit: Number,
    vendor: one of ['netease', 'xiami', 'qq']
  }
  ```

- `musicAPI.getSong(query)`:

  ```
  query: {
    id: String or Number,
    vendor: one of ['netease', 'xiami', 'qq']
  }
  ```

- `musicAPI.getComment(query)`:

  ```
  query: {
    id: String or Number,
    page: Number,
    limit: Number,
    vendor: one of ['netease', 'xiami', 'qq']
  }
  ```

## Warning

1. **It cannot be used for commercial purposes.**
2. **It runs only on NodeJS instead of browser.**
3. **Please use it politely and don't put too much pressure on music platforms.**

## Thanks

The code for this project refers to the following open source projects and has been fixed, improved and added on NodeJS.

1. [listen1_chrome_extension](https://github.com/listen1/listen1_chrome_extension): Has received a lawyer's letter from Tencent. May stop maintenance at the end of the year 2018.
2. [musicAPI](https://github.com/LIU9293/musicAPI): It has stopped maintenance one year ago. So its APIs are invalid.
