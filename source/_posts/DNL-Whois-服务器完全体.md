---
title: DNL Whois 服务器完全体
date: 2026-03-31 02:32:28
---

地址依旧是这个：https://whois.rr.kg/

项目地址：https://github.com/runoneall/dnl-whois-server.git

除了以 TXT 的形式返回数据（点击 Search 按钮）外，我还加入了符合 [RFC 9082](https://datatracker.ietf.org/doc/html/rfc9082) 和 [RFC 9083](https://datatracker.ietf.org/doc/html/rfc9083) 的 RDAP 请求处理，在首页输入框填入 dnl 域名后，会自动在下方生成一段命令

`nicinfo -b https://whois.rr.kg/rdap <domain>`

使用 https://github.com/arineng/nicinfo 进行命令行中的请求
