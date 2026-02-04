---
title: 我制作了一个非常轻量的带桌面 docker 镜像
date: 2026-02-03 12:01:40
---

项目地址： [runoneall/tiny-alpine-desktop: Tiny Alpine desktop](https://github.com/runoneall/tiny-alpine-desktop)

它结合了 [kincsescsaba/remote-desktop-in-docker: Tiny Alpine desktop with "headless" VNC/RDP session and arm support](https://github.com/kincsescsaba/remote-desktop-in-docker) 的 alpine 系统，xrdp 组件和 [docker_headless-vnc/Dockerfile.debian-icewm-vnc at master · alpyol/docker_headless-vnc](https://github.com/alpyol/docker_headless-vnc/blob/master/Dockerfile.debian-icewm-vnc) 的 icewm，但无一例外，都精简掉了浏览器

发布地址： [runoneall/tiny-alpine-desktop - Docker Image](https://hub.docker.com/r/runoneall/tiny-alpine-desktop)

启动时只需 `-e USER_PASSWD=<your_password>` 设置密码即可，端口配置 `-p 3389:3389`

Debian 的版本： [runoneall/tiny-debian-desktop: Tiny Debian desktop](https://github.com/runoneall/tiny-debian-desktop)

---

题外话：谷歌的 firebase 挺好用的啊，这个项目就是在 firebase 上做的，推荐试试
