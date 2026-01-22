---
title: 为 old fashion 主题的新版typecho适配
date: 2024-12-13 19:21:00
tags: []
categories: 默认
---

主题GitHub：https://github.com/MrXiaoM/typecho-theme-old-fashion

# 环境介绍：

- typecho版本：1.2.1
- php版本/扩展：默认

# 主要问题：

- 主题安装后，点击主题设置，会报500错误，`Class 'Typecho\Widget\Helper\Form\Element\TextArea' not found`
- 当在手机端浏览时，搜索框会挡住大部分标题

# 解决方法：

- 将TextArea写法变更为：`\Typecho\Widget\Helper\Form\Element\Textarea`
- 将搜索框移到左侧边栏（当手机端浏览时，侧边栏会被折叠）

# 影响文件：

- functions.php
- header.php
- style.css

# 文件下载

- https://dev.oneall.eu.org/usr/uploads/2024/12/2726861087.zip
