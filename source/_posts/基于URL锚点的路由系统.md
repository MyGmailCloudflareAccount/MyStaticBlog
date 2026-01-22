---
title: 基于URL锚点的路由系统
date: 2025-02-13 09:28:00
tags: []
categories: 默认
---

好处：不会显式刷新，语法简单，使用原生JS

`router.js`

```javascript
class HashRouter {
    constructor(container) {
        this.container = container;
        this.routes = {};

        this.defaultHandler = () => this.show404(this.container);

        this.normalizePath = (path) => {
            if (!path) return "/";
            return path.startsWith("/") ? path : `/${path}`;
        };

        window.addEventListener("hashchange", () => this.handleRoute());
        window.addEventListener("load", () => this.handleRoute());
    }

    addRoute(path, handler) {
        const normalizedPath = this.normalizePath(path);
        this.routes[normalizedPath] = handler;
    }

    setDefaultHandler(handler) {
        this.defaultHandler = handler;
    }

    handleRoute() {
        const rawHash = window.location.hash.slice(1);
        const currentPath = this.normalizePath(rawHash);

        const handler = this.routes[currentPath] || this.defaultHandler;
        this.container.innerHTML = "";
        handler(this.container);
    }

    show404(container) {
        container.innerHTML = "<h1>404 Not Found</h1>";
    }
}
```

`index.html`

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8" />
        <title>HashRouter</title>
    </head>

    <body>
        <h1>HashRouter</h1>

        <div>
            <a href="#/">Home</a>
            <a href="#/about">About</a>
            <a href="#/contact">Contact</a>
            <a href="#/null">Null</a>
        </div>
        <div
            id="main-containter"
            style="border: 1px solid black; margin: auto; padding: 20px;"
        ></div>

        <script src="/router.js"></script>
        <!-- 引入JS文件 -->
        <script>
            // 创建HashRouter实例
            const router = new HashRouter(
                document.getElementById("main-containter"),
            );

            // 定义路由规则
            router.addRoute("/", (containter) => {
                containter.innerHTML = "Welcome to the homepage";
            });
            router.addRoute("/about", (containter) => {
                containter.innerHTML = "About us";
            });
            router.addRoute("/contact", (containter) => {
                containter.innerHTML = "Contact us";
            });

            // 设置默认路由 (若不指定则默认插入 404 Not Found)
            router.setDefaultHandler((containter) => {
                containter.innerHTML = "Page not found";
            });

            // 监听hashchange事件
            router.handleRoute();
        </script>
    </body>
</html>
```

- 创建HashRouter实例时需要传入容器对象`document.getElementById("main-containter")`
- 创建路由时处理函数会被传入 `containter` 代表容器对象
- 运行处理函数前容器对象会被清空
