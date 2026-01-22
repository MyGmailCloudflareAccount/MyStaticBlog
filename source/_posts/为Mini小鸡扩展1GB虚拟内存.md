---
title: 为Mini小鸡扩展1GB虚拟内存
date: 2025-04-24 11:02:00
tags: []
categories: 默认
---

![](https://s.rmimg.com/2025-04-24/1745484882-765922-2025-04-24-165309.png)
是从某个一键脚本抽离出来的

```sh
new_swap=1024  # 单位MB
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

需要root，开机后运行一次就可以了
