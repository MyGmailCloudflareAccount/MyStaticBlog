---
title: 一个简易订阅转换api
date: 2025-01-29 20:05:34
tags: []
categories: 默认
---

仅支持v2ray转clash
https://github.com/runoneall/netlify-v2ray2clash

效果图
![](https://s.rmimg.com/2025-01-30/1738177211-979039-2025-01-30-025909.png)

基于这个项目精简而来
https://github.com/7Sageer/sublink-worker
（主要是给我订阅整合增加clash支持）

demo：https://v2ray2clash.netlify.app/.netlify/functions/clash
发送post请求，把v2ray链接放到body里，返回yaml
