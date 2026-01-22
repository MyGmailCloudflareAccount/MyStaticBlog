---
title: 通过API查询mysql数据库
date: 2024-12-09 12:53:00
tags: []
categories: 默认
---

demo: https://runoneall.serv00.net/Files/mysql-driver.php

效果图:
![](https://s.rmimg.com/2024-11-18/1731916381-650299-2024-11-18-34935.png)
![](https://s.rmimg.com/2024-11-18/1731916384-725557-2024-11-18-35001.png)

代码下载:
https://dev.oneall.eu.org/usr/uploads/2024/12/3853467506.zip

请求方法:

- 发送post请求
- 表单格式
- host指定数据库地址 String
- port指定端口 Int
- username指定用户 String
- password指定密码 String
- database指定数据库 String
- querys指定查询语句 String （多个语句用`;;`隔开）

请求示例:

```python
import requests

data = {
    "host": "<地址>",
    "port": <端口>,
    "username": "<用户名>",
    "password": "<密码>",
    "database": "<数据库名>",
    "querys": "<查询语句>"
}

print(requests.post("https://runoneall.serv00.net/Files/mysql-driver.php", data=data).text)
```

在Cloudflare Worker中使用: (`await sqlQuery("SQL")`)

```js
async function sqlQuery(sqls) {
    const apiUrl = 'https://runoneall.serv00.net/Files/mysql-driver.php'
    const info = {
        host: "<地址>",port: <端口>,username: "<用户名>",
        password: "<密码>",database: "<数据库名>",querys: sqls};
    const formData = new URLSearchParams()
    for (const key in info) {if (info.hasOwnProperty(key)) {formData.append(key, info[key])}}
    const response = await fetch(apiUrl, {method: "POST",
        headers: {'Content-Type': 'application/x-www-form-urlencoded'},
        body: formData.toString()})
    return response.body
}
```
