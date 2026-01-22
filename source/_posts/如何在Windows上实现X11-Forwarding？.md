---
title: 如何在Windows上实现X11 Forwarding？
tags: []
categories: 默认
date: 2025-10-06 13:19:11
---

commit: [windows support · runoneall/nodebus@a1d0505](https://github.com/runoneall/nodebus/commit/a1d05052d642d6a220b432877e25c97eb7017efe)

最终效果如图：

![](https://s.rmimg.com/original/3X/6/0/60f04d146c5025f2d82cfe7a48496b4988e76959.png)

需要配合 VcXsrv 才行，![](https://s.rmimg.com/original/3X/a/f/afd64a476c23bb3420455fdb26295967b24010d8.png)

环境变量 DISPLAY，![](https://s.rmimg.com/original/3X/d/a/da74f4d650f361ae929b13b8c9a41e119e48d009.png)

在 X11 启动时指定 `--trust-x11` 绕过 xauth 验证（Windows上没有）
