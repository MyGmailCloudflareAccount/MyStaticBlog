---
title: 一个用于编辑 cloudflare warp 排除列表的工具
date: 2026-04-11 15:41:49
---

项目地址：https://github.com/runoneall/cfwarp-excludes-editor

> 这个项目使用 [liteloader](https://github.com/runoneall/liteloader) 编写

## 配置 API

1. 新建 config.json 文件，并填写以下内容：

```json
{
    "ACCOUNTID": "Cloudflare 账号 ID",
    "APITOKEN": "API 密钥，需要有 Zero Trust Read/Write 权限"
}
```

2. 运行 main.py

## 示例数据

一个精简过的排除列表，使用 ai 辅助编写

1. 点击 测试API
2. 点击 导入数据
3. 选择 excludes.txt
4. 点击 写入数据
5. 重新连接 Cloudflare WARP
