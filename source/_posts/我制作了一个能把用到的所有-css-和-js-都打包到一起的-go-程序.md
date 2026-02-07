---
title: 我制作了一个能把用到的所有 css 和 js 都打包到一起的 go 程序
date: 2026-02-07 11:46:15
---

下载地址： [Release v1 · runoneall/htmlbuild](https://github.com/runoneall/htmlbuild/releases/tag/v1)

能够自动处理：

1. 从网络中引入的文件，支持解析：`http:` `https:` `//`
2. 从本地引入的文件

支持 `<link rel="stylesheet" href="" />` 和 `<script src=""></script>` 自动转为内联

使用方法：

1. 创建一个 html 文件并编写代码，比如 index.html
2. 使用 htmlbuild index 打包代码，生成 index.min.html
