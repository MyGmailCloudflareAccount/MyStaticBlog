---
title: 在debian-12.8上安装云湖
date: 2024-12-25 03:16:00
tags: []
categories: 默认
---

若你的云湖装不上或出现如下问题

```
yunhu: /lib/x86_64-linux-gnu/libstdc++.so.6: version `GLIBCXX_3.4.32' not found (required by /usr/share/yunhu/lib/libdesktop_drop_plugin.so)
yunhu: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.38' not found (required by /usr/share/yunhu/lib/libflutter_webrtc_plugin.so)
yunhu: /lib/x86_64-linux-gnu/libstdc++.so.6: version `GLIBCXX_3.4.32' not found (required by /usr/share/yunhu/lib/libflutter_webrtc_plugin.so)
```

则可以继续看本文章

这些依赖可以简单的通过apt安装
`mpv`, `libsqlite3-dev:amd64`, `libmpv2:amd64`

这些依赖需要添加软件源安装
`libc6`
软件源: `deb http://deb.debian.org/debian sid main`
参考: https://blog.csdn.net/weixin_45970111/article/details/130842401

这些依赖需要编译安装
`gcc-14.2.0 (libstdc++.so.6.0.33)`
下载: http://ftp.gnu.org/gnu/gcc/gcc-14.2.0/
参考: https://zhuanlan.zhihu.com/p/498529973

(因可能违反Serv00 Tos, 编译好的so文件不再提供, 请前往反馈群云盘的 `linux-yh-libs` 文件夹获取)

然后[前往下载页面](https://www.yhchat.com/c/p/1087)正常安装即可
