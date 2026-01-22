---
title: 一个不多不少刚刚好的短链api实现
tags: []
categories: 默认
date: 2025-11-01 13:58:47
---

这是我用来集成进我博客里的一个api，把不必要的功能全都省略了

```javascript
var whitelist = [];

async function textresp(text, status = 200) {
    return new Response(text, {
        status,
        headers: {
            "Content-Type": "text/plain;charset=UTF-8",
            "Access-Control-Allow-Origin": "*",
        },
    });
}

async function gethash(text, length = 8) {
    const encoder = new TextEncoder();
    const data = encoder.encode(text);

    const hashBuffer = await crypto.subtle.digest("SHA-256", data);
    const hashArray = Array.from(new Uint8Array(hashBuffer));
    const hashHex = hashArray
        .map((b) => b.toString(16).padStart(2, "0"))
        .join("");

    return hashHex.substring(0, length);
}

async function savelink(request, env) {
    if (request.method !== "POST") {
        return await textresp("405 Method Not Allowed", 405);
    }

    var body;

    try {
        const rawbody = await request.arrayBuffer();
        body = new TextDecoder("UTF-8", { fatal: true }).decode(rawbody);
    } catch {
        return await textresp("415 Unsupported Media Type", 415);
    }

    var targeturl;

    try {
        targeturl = new URL(body);
    } catch {
        return await textresp("400 Bad Request (Invalid URL string)", 400);
    }

    if (!whitelist.includes(targeturl.hostname)) {
        return await textresp("403 Forbidden", 403);
    }

    const urlstring = targeturl.toString();
    const hash = await gethash(urlstring);
    await env.shortlink.put(hash, urlstring);

    return new Response(
        JSON.stringify({
            origin: urlstring,
            short: hash,
        }),
        {
            headers: {
                "Content-Type": "application/json;charset=UTF-8",
                "Access-Control-Allow-Origin": "*",
            },
        },
    );
}

async function redirect(request, env) {
    if (request.method !== "GET") {
        return await textresp("405 Method Not Allowed", 405);
    }

    const hash = request.pathname.substring(4);
    const value = (await env.shortlink.get(hash)) ?? "";

    if (value === "") {
        return await textresp("404 Not Found", 404);
    }

    return Response.redirect(value, 302);
}

export default {
    async fetch(request, env, ctx) {
        const url = new URL(request.url);

        if (url.pathname === "/create") {
            return await savelink(request, env);
        }

        if (url.pathname.startsWith("/go/")) {
            request.pathname = url.pathname;
            return await redirect(request, env);
        }

        return await textresp("404 Not Found", 404);
    },
};
```

创建一个kv数据库，名称随意，然后绑定到worker里，注意变量名是 `shortlink`

在 `whitelist` 添加允许短链的 hostname

1. 添加短链

向 `/create` 路由发送 POST 请求，body 为要缩短的完整 URL，返回值为 json，其中 short 字段为原 URL 的哈希值前 8 位

2. 访问短链

访问 `/go/` 后面接上原 URL 的哈希值前 8 位
