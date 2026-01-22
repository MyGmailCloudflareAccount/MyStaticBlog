---
title: Mini小鸡搭一个还算能用的云电脑
date: 2025-04-22 10:40:00
tags: []
categories: 默认
---

浏览网页一点问题没有，配置低的vps也可以看
![](https://s.rmimg.com/2025-04-22/1745310185-392883-2025-04-22-161856.png)

第一步刷轻量系统，这里用alpine 3.20

```sh
wget -O vps.sh https://raw.githubusercontent.com/everett7623/vps_scripts/main/vps.sh && chmod +x vps.sh && clear && ./vps.sh
```

1. 安装桌面（这里用lxqt加lxdm）

```sh
setup-xorg-base
setup-user  # 桌面不能root登录
apk add lxqt-desktop lxqt-core lxqt-panel lxqt-admin lxqt-config lxqt-notificationd lxqt-powermanagement lxqt-themes openbox setxkbmap
addgroup <user> input  # 将新创建的普通用户加入input和video组
addgroup <user> video
apk add dbus dbus-x11 lxdm
rc-update add dbus
rc-update add lxdm
reboot
```

2. 字体和图标

```sh
apk add breeze-icons font-noto-cjk
```

3. 浏览器（火狐算是比较流畅的了）

```sh
apk add firefox
```

4. 开启VNC
   ![](https://s.rmimg.com/2025-04-22/1745311117-804227-2025-04-22-163755.png)

5. 重启系统

```sh
reboot
```
