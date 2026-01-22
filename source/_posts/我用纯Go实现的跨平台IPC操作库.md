---
title: 我用纯Go实现的跨平台IPC操作库
tags: []
categories: 默认
date: 2025-10-18 14:26:50
---

项目地址 [runoneall/pgoipc: 用纯 Go 实现的跨平台 IPC 操作库，包含服务端和客户端](https://github.com/runoneall/pgoipc)

该包支持 `Unix (Domain Socket)` 和 `Windows (Named Pipe)`

该包对 `net` 和 `github.com/Microsoft/go-winio` 进行再封装，使其不用考虑服务器启动和客户端连接方面的逻辑，通过暴露 `conn net.Conn` 对连接进行操作，使其在不同的平台上拥有相同的api

除 README 外，我还写了一个小 demo，通过 ipc 传输 `Stdin` `Stdout` `Stderr`

```golang
package main

import (
	"fmt"
	"io"
	"net"
	"os"
	"os/exec"
	"time"

	"github.com/runoneall/pgoipc/client"
	"github.com/runoneall/pgoipc/server"
)

func startShell(conn net.Conn) {
	proc := exec.Command("sh")

	proc.Stdin = conn
	proc.Stdout = conn
	proc.Stderr = conn

	fmt.Println(proc.Run())
}

func connectShell(conn net.Conn) {
	go io.Copy(conn, os.Stdin)
	io.Copy(os.Stdout, conn)
}

func main() {
	go server.Serv("ipcshell", startShell)

	time.Sleep(time.Second)

	client.Connect("ipcshell", connectShell)
}
```

![](https://s.rmimg.com/original/3X/9/a/9a3390fa90fd1b5d3f839c7f989e2568d146133f.png)
