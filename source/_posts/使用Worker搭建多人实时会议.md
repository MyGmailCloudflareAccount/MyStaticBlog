---
title: 使用Worker搭建多人实时会议
tags: []
categories: 默认
date: 2025-11-08 15:40:10
---

代码非常简单，极容易集成，可以玩玩我的 demo

https://oneall.eu.org/weblive

```javascript
async function textresp(text, status = 200) {
    return new Response(text, {
        status,
        headers: {
            "Content-Type": "text/plain;charset=UTF-8",
            "Access-Control-Allow-Origin": "*",
        },
    });
}

async function getAuthToken(env, name, preset_name = "group_call_participant") {
    const api = `https://api.realtime.cloudflare.com/v2/meetings/${env.meetingId}/participants`;

    const tokenResp = await fetch(api, {
        method: "POST",
        headers: {
            Authorization: env.api_authorization_token,
            "Content-Type": "application/json;charset=UTF-8",
        },
        body: JSON.stringify({
            name: name,
            picture: "https://i.imgur.com/test.jpg",
            preset_name: preset_name,
            client_specific_id: crypto.randomUUID(),
        }),
    });

    const body = await tokenResp.json();

    if (body.success !== true) {
        return await textresp(
            "500 Internal Server Error (Invalid Token Response)",
            500,
        );
    }

    const token = body.data.token;

    return Response.redirect(
        `https://oneall.eu.org/realtimelive?authToken=${token}`,
        302,
    );
}

export default {
    async fetch(request, env, ctx) {
        if (request.method !== "GET") {
            return await textresp("405 Method Not Allowed", 405);
        }

        const url = new URL(request.url);
        const name = url.searchParams.get("name");

        if (!name) {
            return await textresp(
                "400 Bad Request (Invalid URL param: name)",
                400,
            );
        }

        return await getAuthToken(env, name);
    },
};
```

代码需要设置两个环境变量 `meetingId` 和 `api_authorization_token` ，这是教程

https://docs.realtime.cloudflare.com/guides/rest-apis/quickstart

前端搭建代码

[realtimekit-web-examples/html-examples/examples/default-meeting-ui/index.html at main · cloudflare/realtimekit-web-examples](https://github.com/cloudflare/realtimekit-web-examples/blob/main/html-examples/examples/default-meeting-ui/index.html)

如果不想自己搭也可以用我的 `https://oneall.eu.org/realtimelive`
