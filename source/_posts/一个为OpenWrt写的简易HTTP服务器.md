---
title: 一个为OpenWrt写的简易HTTP服务器
tags: []
categories: 默认
date: 2026-01-04 09:19:41
---

用 go 编写，完美跨平台

- `main.go`

```go
package main

import (
	"embed"
	"fmt"
	"net"
	"net/http"
	"os"
	"path/filepath"
	"slices"
	"strings"

	"github.com/gin-gonic/gin"
)

//go:embed embed/*
var embedFS embed.FS

func globalHandler(ctx *gin.Context) {
	host := ctx.Request.Host
	path := ctx.Request.URL.Path

	if len(strings.Split(host, ".")) == 2 && path == "/" {
		getAllSiteHandler(ctx, host)
		return
	}

	dirbase := strings.SplitN(host, ".", 2)[0]
	staticDir := dirbase + ".static"

	if info, err := os.Stat(staticDir); err == nil && info.IsDir() {
		fullPath, err := filepath.Abs(filepath.Join(".", staticDir, path))
		if err != nil {
			ctx.String(500, "%v", err)
			return
		}

		staticHandler(ctx, fullPath)
		return
	}

	ctx.String(404, "404 not found")
}

func getAllSiteHandler(ctx *gin.Context, host string) {
	entries, err := os.ReadDir(".")
	if err != nil {
		ctx.String(500, "%v", err)
	}

	allSite := []string{}

	for _, entry := range entries {
		if entry.IsDir() {
			entryInfo := strings.SplitN(entry.Name(), ".", 2)
			allowSuffix := []string{"static"}

			if len(entryInfo) != 2 {
				continue
			}

			if slices.Contains(allowSuffix, entryInfo[1]) {
				allSite = append(allSite, entryInfo[0])
			}
		}
	}

	if len(allSite) == 0 {
		ctx.String(404, "404 not found")
		return
	}

	ctx.HTML(200, "allSite.html", gin.H{
		"host":    host,
		"allSite": allSite,
	})
}

func staticHandler(ctx *gin.Context, fullPath string) {
	if info, err := os.Stat(fullPath); err == nil && info.IsDir() {
		fullPath = filepath.Join(fullPath, "index.html")
	}

	ctx.File(fullPath)
}

func main() {
	args := os.Args
	if len(args) == 1 {
		fmt.Println("Usage: gowebwrt <ip>")
		return
	}

	r := gin.Default()
	r.LoadHTMLFS(http.FS(embedFS), "embed/*.html")
	r.GET("/*path", globalHandler)
	r.Run(net.JoinHostPort(args[1], "80"))
}
```

- `embed/allSite.html`

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>All Site</title>
    </head>
    <body>
        {{$globalHost := .host}}
        <ul>
            {{range $site := .allSite}}
            <li>
                <a href="//{{$site}}.{{$globalHost}}"
                    >{{$site}}.{{$globalHost}}</a
                >
            </li>
            {{end}}
        </ul>
    </body>
</html>
```

注意：路由器必须支持泛解析，如 `*.www.local`

使用方法：

1. 将打包后的文件放入服务目录，如 `/www/gowebwrt`
2. 赋予可执行权限
3. 新建文件夹，遵循 `<subdomain>.static` 命名规则
4. 入口点文件 `index.html`
5. 访问 `<subdomain>.www.local` 即可看到站点

更改实时生效，无需重启服务端

目前只支持静态文件，后续考虑上 php
