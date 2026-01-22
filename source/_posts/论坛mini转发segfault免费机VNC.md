---
title: 论坛mini转发segfault免费机VNC
tags: []
categories: 默认
date: 2025-08-04 07:34:28
---

最终效果图

![](https://s.rmimg.com/optimized/2X/a/aef0b0c05f1de44effe2d46fe306a407c7f95bf2_2_1306x1000.jpeg)

首先将论坛mini重装成 alpine 3.20（不然没法用）

https://github.com/bin456789/reinstall

segfault免费机申请+保活请看 [segfault免费机全自动保活教程](https://blog.oneall.eu.org/2025/08/03/segfault%E5%85%8D%E8%B4%B9%E6%9C%BA%E5%85%A8%E8%87%AA%E5%8A%A8%E4%BF%9D%E6%B4%BB%E6%95%99%E7%A8%8B/)

需要将密钥导入mini上

首先安装依赖

```shell
apk add screen novnc
```

然后先开启第一个后台

```shell
screen -R segfault-vps
ssh <segfault服务器名>
```

保存退出

再开启一个后台

```shell
screen -R segfault-vps-vnc
novnc_server --listen <网页监听端口> --vnc 127.0.0.1:<ssh转发的vnc端口> --file-only
```

保存退出

然后开放一个端口（值为novnc网页监听端口）

![](https://s.rmimg.com/original/2X/4/4b3ebfb8976015ee21991e1c12ca186a839d295f.png)

到这一步就可以玩耍了，当然如果还想更进一步

首先添加一条A记录到mini的ip

![](https://s.rmimg.com/optimized/2X/f/fdaf17369808529d7ac44a98f2a29e889051e259_2_1380x52.png)

然后添加一条 Origin Rules

名称随便写，比如 `fnl-mini segfault-vps novnc`

![](https://s.rmimg.com/optimized/2X/7/7e7c967bdfea511bcbb43de2cd7ce73e0977dff4_2_1380x548.png)

![](https://s.rmimg.com/original/2X/b/b6b00310eb2f339fde06c93238c9a7bb9450ef44.png)

保存退出，现在就可以通过 `https://你的自定义域名/vnc.html` 连接了
