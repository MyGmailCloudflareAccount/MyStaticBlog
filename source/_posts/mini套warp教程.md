---
title: mini套warp教程
tags: []
categories: 默认
date: 2025-08-17 07:22:47
---

该教程参考了这篇文章：https://p3terx.com/archives/use-cloudflare-warp-to-add-extra-ipv4-or-ipv6-network-support-to-vps-servers-for-free.html

通过 `ip a` 查看 eth0 网卡，应该大体如下所示

```plaintext
eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:72:0f:c1:ba:a4 brd ff:ff:ff:ff:ff:ff
    inet 192.168.200.80/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::272:fff:fec1:baa4/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
```

其中 `inet` 代表ipv4地址 `inet6` 代表ipv6地址，由此可以得到，mini采用 vpc内网方案 v4v6双栈网络（v6用不了，但内网有v6）

首先安装wgcf

```shell
mkdir ~/warp && cd ~/warp
wget https://github.com/ViRb3/wgcf/releases/download/v2.2.28/wgcf_2.2.28_linux_amd64
mv wgcf_2.2.28_linux_amd64 wgcf
chmod +x wgcf
./wgcf register
./wgcf generate
```

编辑 `wgcf-profile.conf`

1. 解析ip

使用 `nslookup engage.cloudflareclient.com` 将得到一个v4地址和一个v6地址，这里用v4

将 `engage.cloudflareclient.com` 替换为v4的地址

2. 在 `[Interface]` 和 `[Peer]` 之间加入

```plaintext
PostUp = ip -4 rule add from <替换IPv4地址> lookup main
PostDown = ip -4 rule delete from <替换IPv4地址> lookup main
PostUp = ip -6 rule add from <替换IPv6地址> lookup main
PostDown = ip -6 rule delete from <替换IPv6地址> lookup main
```

其中ipv4和ipv6指的是内网地址，分别是 inet(去掉/24) 和 inet6(去掉/64)

3. 安装wireguard（alpine3.20）

该章节参考了这篇文章：https://wiki.alpinelinux.org/wiki/Configure_a_Wireguard_interface_(wg)

```shell
apk add wireguard-tools-wg-quick iptables
mkdir -p /etc/wireguard
cp ~/warp/wgcf-profile.conf /etc/wireguard/wgcf.conf
wg-quick up wgcf
```

4. 验证

```shell
curl -6 ip.p3terx.com
curl -4 ip.p3terx.com
```

出现 `CLOUDFLARENET` 就算成功
