---
title: alpine的alist服务脚本
date: 2025-04-30 04:32:00
tags: []
categories: 默认
---

此是对 https://dev.oneall.eu.org/archives/63 的补充
原帖用的 screen 来启动 alist，但缺点是开机得手动启动

于是我参考了这篇帖子 https://mailberry.com.cn/2024/12/alpine-install-alist
实测不可用

于是我开始对原贴脚本进行修改
`/etc/init.d/alist`

```shell
#!/sbin/openrc-run

name="alist"
description="Alist service"
pidfile="/var/run/alist.pid"
directory="/root/alistrun"
command="./alist"
command_args="server"

depend() {
    need net
}

start_pre() {
    checkpath --directory --owner root:root --mode 0755 "$directory"
}

start() {
    ebegin "Starting $name"
    cd "$directory" || return 1
    nohup $command $command_args >/dev/null 2>&1 &
    echo $! > "$pidfile"
    eend $?
}

stop() {
    ebegin "Stopping $name"
    if [ -f "$pidfile" ]; then
        kill $(cat "$pidfile") 2>/dev/null
        rm -f "$pidfile"
    fi
    eend $?
}
```

```shell
chmod +x /etc/init.d/alist
rc-update add alist
```

启动：`rc-service alist start`
停止：`rc-service alist stop`
状态：`rc-service alist status`
