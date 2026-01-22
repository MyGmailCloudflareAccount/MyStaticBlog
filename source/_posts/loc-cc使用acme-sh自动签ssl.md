---
title: loc.cc使用acme.sh自动签ssl
date: 2025-06-04 13:14:47
tags: []
categories: 默认
---

关于如何添加 ns 记录，目前除手动同步两边记录外并未有好方法，改 SOA 并未生效
关于 ssl 的问题，可以使用 webroot 验证，确保域名能够访问
此处用 serv00 演示，因为可以用 devil 自动添加证书

演示：
![](https://s.rmimg.com/original/2X/5/562d06ecc9ce277082114386219d479ad64d70bc.png)

1. 安装 acme.sh

```shell
curl https://get.acme.sh | sh -s email=你的邮箱
```

2. 设置验证路径

```shell
mkdir -p ~/ssl/update-sh/ # 用于存放更新脚本
mkdir -p ~/domains/你的域名/public_html/.well-known/acme-challenge # 用于webroot验证
```

3. 自动更新脚本（注意要用 `fullchain` 否则不完整）

```shell
#!/usr/local/bin/bash

DOMAIN="你的域名"
IP="128.204.223.46"
CERT_DIR="~/ssl"

devil ssl www del $IP $DOMAIN
devil ssl www add $IP "$CERT_DIR/$DOMAIN.fullchain.crt" "$CERT_DIR/$DOMAIN.key" $DOMAIN
```

4. 添加证书

```shell
acme.sh --issue -d 你的域名 \
    -w ~/domains/你的域名/public_html \
    --cert-file ~/ssl/你的域名.crt \
    --key-file ~/ssl/你的域名.key \
    --fullchain-file ~/ssl/你的域名.fullchain.crt \
    --reloadcmd "~/ssl/update-sh/你的域名.sh"
```

5. 自动续期（如果没有）

```shell
acme.sh --install-cronjob
```
