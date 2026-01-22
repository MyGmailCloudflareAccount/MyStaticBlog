---
title: 记一次对IE浏览器的兼容
date: 2025-01-04 05:13:00
tags: []
categories: 默认
---

能到IE7，但没截图，先用个IE8的截图吧
用的在线IE浏览器

![](https://s.rmimg.com/2025-01-04/1735963406-821520-browserling-screenshot-1.png)
![](https://s.rmimg.com/2025-01-04/1735963415-666308-browserling-screenshot.png)

问题最大的地方是友链页面（links）
因不支持flex布局，所以“升级”到了float布局，兼容性+1

以及首页的标题区域和文章区域的间隔，这个可以使用margin-top解决，只在IE浏览器访问时在文章区域加入 `margin-top: 40px;`
搜索框在IE7中会出bug，但是免费时间用完了，修不了

当时的截图（IE7）
![](https://s.rmimg.com/2025-01-04/1735963912-277278-4ada49847ea549faa435d09724db6c63.png)
