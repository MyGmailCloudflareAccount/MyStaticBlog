---
title: 低版本mac装docker
date: 2025-04-09 08:35:35
tags: []
categories: 默认
---

翻到台mac mini，运行macOS 10.15，已经不支持安装docker GUI了

首先安装brew

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装docker cli

```zsh
brew install docker docker-compose
```

安装colima做docker后端

```zsh
brew install colima
```

编译安装qemu（我用的7.2.0）

```zsh
brew install pkg-config glib pixman ninja cmake wget sdl2 lzo
wget https://download.qemu.org/qemu-7.2.0.tar.xz
tar -xf qemu-7.2.0.tar.xz
rm qemu-7.2.0.tar.xz && cd qemu-7.2.0
./configure --target-list=x86_64-softmmu,aarch64-softmmu --enable-debug --enable-sdl --enable-vnc
make -j$(sysctl -n hw.logicalcpu)
sudo make install
cd .. && rm -rf qemu-7.2.0
qemu-system-x86_64 --version
```

启动colima

```zsh
colima start  # 后面看着给 --cpu 核心数 --memory 内存(GB)
```

运行 `docker ps` 查看是否安装成功（列表可能为空，无报错就是成功了）
