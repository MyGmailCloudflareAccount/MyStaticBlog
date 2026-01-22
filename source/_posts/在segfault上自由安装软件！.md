---
title: 在segfault上自由安装软件！
tags: []
categories: 默认
date: 2025-08-19 06:11:14
---

GitHub地址：https://github.com/runoneall/sfpkg

基于udocker实现，虽是容器，但与宿主机高度衔接

可互通：网络端口，当前目录（及子目录），桌面环境

注意！九分有十分的不建议在非主目录 `/sec/root` 下执行 `sfpkg` ！

1. 安装

```shell
wget https://github.com/runoneall/sfpkg/releases/download/v2/sfpkg
chmod +x sfpkg
mv sfpkg /sec/usr/bin
```

键入 `sfpkg -h` 查看是否安装成功

2. 初始化容器

输入 `sfpkg init` 初始化容器，中途也许会爆几个 `Error` 先不用管
等待完成后，输入 `udocker ps |grep sfpkg-container` 若有输出则代表成功
若要重新安装也是 `sfpkg init`

3. 安装软件

因为容器使用的是alpine系统，所以使用apk包管理器
输入 `sfpkg apk add <软件名>` 即可进行安装（可携带参数）
输入 `sfpkg apk` 可获取帮助信息
示例：`sfpkg apk add fastfetch --no-cache`

4. 运行软件

使用 `sfpkg run <软件名>` 即可运行（可携带参数）
示例：`sfpkg run sh`

5. 创建快捷方式

若要将已安装的软件链接到宿主机，可以使用 `sfpkg link <软件名>`
若宿主机已安装了同名软件，新软件将被安装为 `sf<软件名>`
软件将被链接到 `/sec/usr/bin` 可以使用 `ls /sec/usr/bin` 查看已连接的软件列表
可以使用 `rm /sec/usr/bin/<软件名>` 删除链接
