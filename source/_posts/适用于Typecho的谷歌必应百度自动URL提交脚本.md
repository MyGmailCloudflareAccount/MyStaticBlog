---
title: 适用于Typecho的谷歌必应百度自动URL提交脚本
date: 2024-12-19 10:28:00
tags: []
categories: 默认
---

# 仅适用于使用mysql数据库安装的typecho且使用默认URL结构！

效果图：
![](https://s.rmimg.com/2024-12-19/1734598864-426541-2024-12-19-165530.png)

参考：
https://blog.csdn.net/qq_36905522/article/details/136024994

依赖：

- python3.x
- python内置包：json，urllib
- python第三方包：requests，httplib2，oauth2client
- mysql-driver：https://dev.oneall.eu.org/archives/15/（若你是serv00 s2也可以直接用我提供的地址）
- 将下载的Google Index Api Key（一般为 `gen-lang-client-xxx.json` ）保存为 `GoogleIndexApiKey.json`
- 准备必应和百度的API token

代码：

```python
import json
from urllib.parse import quote
from oauth2client.service_account import ServiceAccountCredentials
import httplib2
import requests

driver_url = " mysql-driver地址 "
query_info = {
    "host": "typecho博客安装数据库地址",
    "port": "typecho博客安装数据库端口",
    "username": "typecho博客安装数据库用户名",
    "password": "typecho博客安装数据库密码",
    "database": "typecho博客安装数据库名",
    "querys": "SELECT * FROM typecho_contents"
}
response = requests.post(driver_url, query_info)

article = response.json()[0]["results"]
blog_link_base = "博客主页地址"  #结尾不要加 /，如 https://dev.oneall.eu.org
links = [blog_link_base, ]
for item in article:
    article_cid = item["cid"]
    article_type = item["type"]
    article_slug = item["slug"]
    if article_type == "post":
        links.append(f"{blog_link_base}/archives/{article_cid}/")
        continue
    if article_type == "page":
        links.append(f"{blog_link_base}/{article_slug}.html")
        continue

bing_index_api_url = "https://www.bing.com/indexnow?url={}&key=必应API Key"
baidu_index_api_url = f"http://data.zz.baidu.com/urls?site={blog_link_base}&token=百度API Token"
google_index_scopes = ["https://www.googleapis.com/auth/indexing", ]
google_index_endpoint = "https://indexing.googleapis.com/v3/urlNotifications:publish"
google_index_key = "GoogleIndexApiKey.json"
google_index_credentials = ServiceAccountCredentials.from_json_keyfile_name(google_index_key, scopes=google_index_scopes)
google_index_http = google_index_credentials.authorize(httplib2.Http())
for link in links:
    print("\nIndexing:", link)
    print("  Bing:", end=" ")
    response = requests.get(bing_index_api_url.format(quote(link)))
    print(response.status_code)
    print("  Baidu:", end=" ")
    response = requests.post(baidu_index_api_url, quote(link))
    print(response.status_code)
    print("  Google:", end=" ")
    google_index_content = json.dumps({
        "url": link,
        "type": "URL_UPDATED"
    })
    response, content = google_index_http.request(google_index_endpoint, method="POST", body=google_index_content)
    print(response["status"])
```
