---
title: 使用nodebus批量管理多个服务器
tags: []
categories: 默认
date: 2025-08-29 15:09:12
---

nodebus 是我写的一个工具，可以自动ssh登录并执行命令

地址：https://github.com/runoneall/nodebus

起因是在 orgv.eu 中遇到的，团队有三台服务器，两台 2c2g 是同版本 ubuntu，一台 4c8g 是 debian，现在要给这三台安装docker，并对三台服务器的docker进行管理，一个一个ssh确实很麻烦，于是就写了这个工具

我将两台 2c2g 命名为 node0 和 node2，一台 4c8g 是 node1

因为我 node1 已经安装了，这里给 node0 和 node2 安装 docker

```shell
./nodebus --node node0,node2 run 'for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done'

./nodebus --node node0,node2 run 'sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update'

./nodebus --node node0,node2 run 'sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin'
```

验证安装

```shell
./nodebus --node node0,node1,node2 run docker ps
```

完全没问题

另外，这个工具另一个用处就是批量管理多个docker，所以我加了一个快捷方式

```shell
./nodebus --node node0,node1,node2 docker ps
```

也是可以的，用法和官方docker完全一样

还有一点，所有的命令运行都支持pty，所以可以进行交互，例如

```shell
./nodebus --node node0,node1,node2 run bash
```

即可进入bash shell
