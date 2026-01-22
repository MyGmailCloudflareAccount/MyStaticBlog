---
title: vercel搭建跨域API
date: 2025-03-23 07:34:00
tags: []
categories: 默认
---

使用 `vercel edge function`

```typescript
export const runtime = "edge";

async function handleRequest(req: Request) {
    const url = new URL(req.url);
    const targetUrl = decodeURIComponent(url.search).slice(1);

    const reqInit: RequestInit & { duplex?: string } = {
        headers: req.headers,
        method: req.method,
        body: req.body,
        redirect: "follow",
        duplex: "half",
    };
    const modifiedRequest = new Request(targetUrl, reqInit);
    const resp = await fetch(modifiedRequest);

    return new Response(resp.body, {
        status: resp.status,
        statusText: resp.statusText,
        headers: new Headers({
            ...resp.headers,
            "Cache-Control": "no-store",
            "Access-Control-Allow-Methods":
                "GET, POST, PUT, DELETE, PATCH, OPTIONS",
            "Access-Control-Allow-Headers": "*",
            "Access-Control-Allow-Origin": "*",
        }),
    });
}

export const GET = handleRequest;
export const POST = handleRequest;
export const PUT = handleRequest;
export const DELETE = handleRequest;
export const PATCH = handleRequest;
export const OPTIONS = handleRequest;
```

保存为 `vap.ts` (名称随意)
API地址: `<域名>/api/<保存名称>?<目标URL>`
