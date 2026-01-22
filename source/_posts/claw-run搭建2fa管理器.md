---
title: claw run搭建2fa管理器
date: 2025-06-06 10:38:46
tags: []
categories: 默认
---

好处：
除前端外基本没有网络开销，而且还很实用（可能是最有用的方式？）

![](https://s.rmimg.com/optimized/2X/3/32f81fbe5df763e1753c501a1fce40c6f1f68949_2_1380x174.png)

GitHub 地址：
https://github.com/quicklyon/2FAuth-docker

搭建：

- Image：`easysoft/2fauth:latest`
- Usage：`Fixed`
- CPU：`0.1`
- Memory：`128M`
- Port：`8000`
- Local Storage：`2G`

费用：
![](https://s.rmimg.com/original/2X/3/314cb4fad6aa1887dd8e296a855707595ebba15c.png)
`0.03*31=0.93`，完全够

后台管理：
因为目前这个 image 还有些 bug，所以得手动允许 CORS 和 Mixed Content（期待大佬修复）

`chrome://flags` 找到 `Insecure origins treated as secure` 添加 `http://你的域名` 并 `Enable`
浏览器添加允许跨域插件并启动

进入后台后点击底部的设置，往下滑找到 `禁用注册`
![](https://s.rmimg.com/original/2X/d/d585263e34f51fd729874eb9807db142dd51b8f4.png)
