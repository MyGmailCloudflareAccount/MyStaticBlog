---
title: MiWiFi 3G(R3G) 一站式刷机
date: 2024-12-09 11:37:18
tags: []
categories: 默认
---

- 本教程提供的文件有可能不是最新的, 并且有可能过时, 请注意甄别
- 本教程参考 [小米路由器3G R3G 刷入Breed和OpenWrt 20.02.2 的记录](https://www.cnblogs.com/milton/p/16163521.html), [小米路由器 3G 使用 OpenWRT](https://www.ohyee.cc/post/note_miwifi_openwrt), [小米R3G的USB挂载问题](https://www.right.com.cn/FORUM/thread-836975-1-1.html)

# 准备

- R3G路由器
- 电脑, 安装 `python3` 环境
- 安装了 `requests` 依赖包
- 全程使用有线连接 (!!! 不要用无线 !!!)
- VPN (也许?)
- !!! 眼睛与脑子 !!!

# 解锁并获取权限

1. 下载开发版系统 [下载地址](https://bigota.miwifi.com/xiaoqiang/rom/r3g/miwifi_r3g_firmware_12f97_2.25.124.bin)
2. 进入到路由器管理, 选择 `系统升级` - `本地升级` - `上传固件`, 选择下载的文件, 等待路由器更新重启完成
3. 登入到路由器后台, 查看是否为开发版系统, 如果是, 进行下一步, 否则重来或寻找其他方法
4. 下载解锁工具 [下载地址](https://github.com/acecilia/OpenWRTInvasion/archive/refs/tags/0.0.8.zip)
5. 解压并进入, 运行 `pip3 install -r requirements.txt && python3 remote_command_execution_vulnerability.py` 并填入信息, 等待一段时间后解锁成功, 然后保存 `FTP` 和 `SSH 或 Telnet` 的访问信息 (用户名 `root` 密码 `root` )

# 刷入不死Breed

1. 使用SSH或Telnet登入到路由器 (以下简称 `登入到路由器` )
2. 使用 `cat /proc/mtd` 查看闪存布局
3. 使用 `dd if=/dev/mtd4 of=/tmp/eeprom.bin` 备份 `eeprom`
4. 使用FTP登入到路由器, 进入 `/tmp` 目录, 把 `eeprom.bin` 下载到本地存储
5. 下载Breed [下载地址](https://breed.hackpascal.net/breed-mt7621-xiaomi-r3g.bin) 并上传到路由器 `/tmp` 目录
6. 登入到路由器, 使用 `cd /tmp` 进入到目录, 使用 `mtd -r write /tmp/breed-R3G.bin Bootloader` 刷入Breed, 完成后路由器会自动重启, 等待重启完成

# 刷入OpenWrt

1. 断开路由器电源, 按住 `Reset` 并重新接入电源, 当网口灯闪烁3次后浏览器输入 `192.168.1.1` 进入Breed管理面板
2. 点击 `Mac地址修改` 查看是否全为 `FF`, 如果是, 点击固件更新, 在 `eeprom` 处上传备份的 `eeprom` 文件
3. 下载OpenWrt固件 [下载地址](https://firmware-selector.openwrt.org/?version=23.05.4&target=ramips%2Fmt7621&id=xiaomi_mi-router-3g), 进入后找到版本选择, 点击 `非SNAPSHOT` 的最新版本, 下载 `KERNEL1` 和 `ROOTFS0`
4. 进入Breed, 点击环境变量, 删除 `normal_firmware_md5`
5. 进入Breed, 选择固件更新, 在下拉框中选择 `Openwrt` , 按照提示上传 `KERNEL1` 和 `ROOTFS0` 文件, 点击自动重启, 稍等片刻即可安装完成

# 汉化

1. 进入 `System` - `Software`
2. 点击 `Update lists` 等待完成 (可能较慢, 如有动手能力可以点击 `Configure opkg` 自行换源)
3. 在 `Filter` 处输入 `luci-i18n-base-zh-cn` 并等待安装完成
4. 刷新页面

# 识别USB存储设备

1. 登入到路由器
2. 先运行 `opkg install kmod-nls-cp437 kmod-nls-iso8859-1 kmod-usb-core kmod-usb-ohci kmod-usb-storage kmod-usb2 mount-utils`
    - 当你的盘为fat格式时运行 `opkg install kmod-fs-vfat`
    - 当你的盘为ext3格式时运行 `opkg install kmod-fs-ext3`
    - 当你的盘为ntfs格式时运行 `opkg install ntfs-3g`
    - 当你的盘为ext4格式时运行 `opkg install kmod-fs-ext4`
3. 挂载
    - 一般为 `/dev/sda1`
    - 列出所有可用设备 `ls /dev | grep sda`
    - 使用 `mount /dev/sda1 /mnt` 将设备挂载到 `/mnt` 目录
    - 使用 `cd /mnt` 进入到设备
    - 使用 `umount /mnt` 取消挂载 (!!! 拔出设备前一定要做 !!!)
    - 路由器重启后需要重新挂载
4. 当挂载设备耗电较大 (如硬盘盒外接硬盘) 时, 建议使用外接供电, 否则路由器容易供电不足
