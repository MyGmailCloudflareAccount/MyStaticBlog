---
title: 一个Go写的端口转发工具，非常适合NAT服务器使用
tags: []
categories: 默认
date: 2026-01-08 09:20:13
---

起因是 shareNL 上的 incus 机不能跑docker，在 @RodickYu 的提醒下发现不能使用端口绑定，必须使用 --network host 来启动，但这样以来对于 nat 服务器就特别的不友好，所以有了这个工具

port link 通过一个后台服务端，维护多条端口转发，同时绑定 tcp 和 udp，可以将远程转发到本地，或将本地转发到另一个本地

项目地址： [runoneall/portlink: 纯 Golang 编写端口转发工具，用于将远程端口转发到本地](https://github.com/runoneall/portlink)
