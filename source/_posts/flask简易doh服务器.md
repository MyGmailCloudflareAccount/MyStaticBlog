---
title: flask简易doh服务器
date: 2025-01-16 16:46:00
tags: []
categories: 默认
---

```python
import base64
import socket
import random
from urllib import request as urllib_request
from urllib import parse as urllib_parse
from flask import Flask, make_response
from flask import request as flask_request

app = Flask(__name__)
socket.setdefaulttimeout(5)
dnss = [
    "8.8.8.8",
    "8.8.4.4",
    "1.1.1.1",
    "1.0.0.1"
]
jsonapis = [
    "https://cloudflare-dns.com/dns-query",
    "https://dns.google/resolve"
]

def send_dns_query(dns_query: bytes) -> bytes:
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.sendto(dns_query, (random.choice(dnss), 53))
    rx, _ = sock.recvfrom(4096)
    sock.close()
    return rx

@app.route("/dns-query", methods=["GET", "POST"])
def dns_query():
    if "Content-Type" not in flask_request.headers or flask_request.headers["Content-Type"] != "application/dns-message":
        return make_response("Invalid Content-Type", 400, {"Content-Type": "text/plain; charset=utf-8"})
    if flask_request.method == "GET":
        dns_query = base64.urlsafe_b64decode(flask_request.args.get("dns") + "===")
        dns_response = send_dns_query(dns_query)
        return make_response(dns_response, 200, {"Content-Type": "application/dns-message"})
    if flask_request.method == "POST":
        dns_query = flask_request.stream.read()
        dns_response = send_dns_query(dns_query)
        return make_response(dns_response, 200, {"Content-Type": "application/dns-message"})
    return make_response("Invalid Method", 405, {"Content-Type": "text/plain; charset=utf-8"})

@app.route("/resolve", methods=["GET"])
def dns_json():
    if "Accept" not in flask_request.headers or flask_request.headers["Accept"] != "application/dns-json":
        return make_response("Invalid Accept", 400, {"Content-Type": "text/plain; charset=utf-8"})
    target_domain = flask_request.args.get("name")
    target_type = flask_request.args.get("type")
    if not target_domain or not target_type:
        return make_response("Invalid Request", 400, {"Content-Type": "text/plain; charset=utf-8"})
    query_url = random.choice(jsonapis) + "?name=" + urllib_parse.quote(target_domain) + "&type=" + urllib_parse.quote(target_type)
    req_headers = {"Accept": "application/dns-json"}
    req = urllib_request.Request(query_url, headers=req_headers)
    rep = urllib_request.urlopen(req)
    rep_status = rep.getcode()
    rep_content_type = rep.headers.get('Content-Type')
    rep_body = rep.read()
    rep.close()
    return make_response(rep_body, rep_status, {"Content-Type": rep_content_type})

if __name__ == "__main__":
    app.run()
```

全类型支持：普通dns查询，jsonapi
普通dns查询地址：`/dns-query`
jsonapi查询地址：`/resolve`

demo：https://doh.xserver.rr.nu
