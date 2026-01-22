---
title: alpine3.20安装code-server
tags: []
categories: 默认
date: 2025-09-01 08:42:07
---

占用预览

![](https://s.rmimg.com/original/2X/4/4063e63e5b7827a658f76040363a69de91c554a8.png)

提前加 2gb swap

```shell
#!/bin/sh
new_swap=2048
swap_partitions=$(grep -E '^/dev/' /proc/swaps | awk '{print $1}')
for partition in $swap_partitions; do
    swapoff "$partition"
    wipefs -a "$partition"
    mkswap -f "$partition"
done
swapoff /swapfile
rm -f /swapfile
dd if=/dev/zero of=/swapfile bs=1M count=$new_swap
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

然后安装依赖包

```shell
apk add libstdc++ libc6-compat python3 make g++ pkgconfig fontconfig git krb5-dev
apk add nodejs npm yarn
apk cache clean
```

使用 yarn 安装 4.96.2 版本的 code-server（最后一个支持node v20的）

```shell
yarn global add code-server@4.96.2
```

最后启动服务即可

```shell
code-server
```
