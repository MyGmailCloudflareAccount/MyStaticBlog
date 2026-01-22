---
title: 自建doh服务器+设置Firefox使用
date: 2024-12-09 11:42:50
tags: []
categories: 默认
---

# 前情提要

- 本教程不会详细解释各名词含义, 需要了解自行搜索
- 本教程有些步骤需要自行搜索, 肯定都有答案, 站长不想再写一遍
- 本教程站长亲测可用, 但因网络环境差异不保证可用性
- 本教程适用于Android, 但也支持其他平台(可能会有出入)

# 准备

- Cloudflare账号
- Github账号
- Android手机
- (可选) Google Play
- (可选) 一个域名
- (可选) VPN
- (必须) 脑子与眼睛

# 搭建doh

1. 进入Github
2. Fork仓库: [tina-hello/doh-cf-workers](https://github.com/tina-hello/doh-cf-workers)
3. 等待完成
4. 从 README 中找到 `Deploy with Workers`
5. 跟随提示完成操作
6. 打开 `Pages and Workers` 页面
7. 找到名为 `doh` 的Worker并点击
8. 点击编辑代码, 找到名为`path`的全局变量
    - `path`: 入口点, 默认为允许所有, 但所有人可用, 建议改为 `/`+`随便填, 必须为英文`
9. (如果有域名且绑定到了CF) 退出编辑, 转到 `设置` - `触发器` - `自定义域` - `添加自定义域` 绑定自定义域
10. 将 `Worker地址` 与 `第8步修改的路径` 拼接在一起就是你的 `doh服务器地址`
11. 返回Github, 转到存储库页面, 点击 `Action`, 把里面你能找到的所有记录都删掉 (如果你不想公开你的doh服务器地址)

# 设置Firefox

1. 此章节参考: [ESNI和加密DNS - 保护信息隐私的最后一块拼图](https://blog.17lai.site/posts/ec2cad2)
2. 从 `Google Play` 上安装最新版的 `Firefox Nightly` 应用 (或你知道的其他地方) ([官方网站](https://www.mozilla.org/zh-CN/firefox/nightly/all/))
3. 打开Firefox Nightly, 完成一些设置后在地址栏输入 `about:config`
    1. 点击上方搜索框
    2. 输入 `network.trr.mode` 将数值改为3或2 (`0`对应的是不开启此功能; `1`对应的是交由浏览器选择DoH与传统方式那种更快; `2`代表优先使用加密DNS查询, 如果失败则回落到普通DNS查询; `3`代表只使用加密DNS查询; `5`代表明确的关闭此功能)
    3. 输入 `network.trr.uri` 并输入之前获取的doh服务器地址
    4. 输入 `network.security.esni.enabled` (默认没有, 点击新建) 类型选 `布尔值`, 值为 `true`
    5. 所有更改会自动保存, 可以通过刷新页面查看是否保存
4. 完全退出Firefox Nightly后重新打开, 更改生效
5. 完成, 可前往 [在线检测页面](https://encryptedsni.com/) 查看效果, 点击 `检测我的浏览器` 并看到四个对勾即为成功
