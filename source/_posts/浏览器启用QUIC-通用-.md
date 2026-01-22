---
title: 浏览器启用QUIC（通用）
date: 2024-12-09 11:41:54
tags: []
categories: 默认
---

- 此文章是对 [自建doh服务器+设置Firefox使用](https://runoneall.github.io/自建doh服务器-设置Firefox使用.html) 文章的补充
- 此教程适用于大部分基于 `Gecko内核` 和 `Chromium内核` 的浏览器
- QUIC于 `2024.10.3` 还处于试验阶段，部分网站不支持QUIC

# Chromium内核

1. 在地址栏输入 `chrome://flags` ( `MicrosoftEdge` 为 `edge://flags` )
2. 找到 `enable-quic`
3. 设置为 `Enabled`

# Gecko内核

1. 在地址栏输入 `about:config`
2. 找到 `network.http.http3.enabled` (没有则新建，类型为 `bool` )
3. 设置为 `true`

# 检测

1. !!完全退出!! 浏览器 (不然部分浏览器会不生效)
2. 重新打开浏览器，前往 [quic.nginx.org](https://quic.nginx.org/)
3. 等待检测完成
