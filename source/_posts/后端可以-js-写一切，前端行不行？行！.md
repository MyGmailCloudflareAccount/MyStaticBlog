---
title: 后端可以 js 写一切，前端行不行？行！
date: 2026-05-01 14:37:37
---

今天我突发奇想，于是就写了个小工具，专门用于解决在浏览器原生 js 中操作 html 过于麻烦的问题

下面是源代码：

```javascript
const Node = (node) => {
    return new (class {
        constructor(node) {
            if (typeof node === "string") {
                this.node = document.querySelector(node);
            } else {
                this.node = node;
            }
        }

        empty() {
            this.node.innerHTML = "";
        }

        __setAttr(node, attrs) {
            for (const [key, value] of Object.entries(attrs)) {
                if (key === "style" && typeof value === "object") {
                    Object.assign(node.style, value);
                } else if (key in node) {
                    node[key] = value;
                } else {
                    node.setAttribute(key, String(value));
                }
            }
        }

        setAttr(attrs = {}) {
            this.__setAttr(this.node, attrs);
        }

        addNode(tag, attrs = {}) {
            const newNode = document.createElement(tag);
            this.__setAttr(newNode, attrs);
            this.node.appendChild(newNode);
            return new this.constructor(newNode);
        }

        async __fetchURL(url) {
            const resp = await fetch(url);
            return await resp.text();
        }

        async importHTML(url) {
            const html = await this.__fetchURL(url);
            this.node.innerHTML += html;
        }

        async importCSS(url) {
            const css = await this.__fetchURL(url);
            this.addNode("style", { innerHTML: css });
        }

        async importJS(url) {
            const js = await this.__fetchURL(url);
            this.addNode("script", { innerHTML: js });
        }

        destroy() {
            this.node.remove();
        }
    })(node);
};
```

这个工具可以：

1. 操作节点属性
2. 添加子节点
3. 导入 html, css, js

意外的感觉还有点用呢...

来个效果演示：

```javascript
(async () => {
    const head = Node("head");
    head.addNode("meta", { charset: "utf-8" });
    head.addNode("meta", { name: "renderer", content: "webkit" });
    head.addNode("meta", {
        name: "viewport",
        content: "width=device-width, initial-scale=1",
    });
    head.addNode("link", { rel: "icon", href: "/favicon.ico" });

    const body = Node("body");
    body.setAttr({
        style: {
            margin: "0",
        },
    });

    window.root = body.addNode("div", { id: "root" });
    root.setAttr({
        style: {
            width: "100vw",
            height: "100vh",
            overflow: "hidden",
            display: "flex",
            flexDirection: "column",
            backgroundColor: "#eee",
        },
    });

    await root.importJS("/component/header.js");
    await root.importJS("/component/container.js");
    await root.importJS("/component/footer.js");
})();
```

```javascript
{
    const container = root.addNode("div", {
        id: "container",
        style: {
            flex: "1",
            display: "flex",
            columnGap: "10px",
        },
    });

    const sideWidth = "250px";
    window.side = container.addNode("div", {
        id: "side",
        style: {
            width: sideWidth,
            backgroundColor: "#eee",
        },
    });
    side.importJS("/component/side.js");

    if (window.innerWidth < 768) {
        side.setAttr({
            style: {
                height: "100%",
                boxShadow: "inset -2px 0 5px rgba(0, 0, 0, 0.6)",
                position: "absolute",
                top: "0",
            },
        });

        const collapse = container.addNode("button", {
            id: "side-collapse",
            style: {
                position: "absolute",
                top: "10px",
                border: "0",
                borderRadius: "0 50% 50% 0",
                boxShadow: "0 0 10px rgba(0, 0, 0, 0.6)",
                padding: "5px",
                paddingRight: "15px",
            },
        });

        const flag = collapse.addNode("input", {
            id: "collapse-flag",
            type: "checkbox",
            style: { display: "none" },
        }).node;

        collapse.addNode("label", {
            id: "collapse-label",
            for: "collapse-flag",
            innerText: "≡",
            style: {
                fontSize: "30px",
                marginLeft: "10px",
            },
        });

        const toggle = () => {
            side.setAttr({
                style: {
                    display: flag.checked ? "block" : "none",
                },
            });

            collapse.setAttr({
                style: {
                    left: flag.checked ? sideWidth : "0",
                },
            });
        };

        collapse.node.addEventListener("click", toggle);
        toggle();
    }

    window.main = container.addNode("div", {
        id: "main",
        style: {
            flex: "1",
        },
    });
    main.importJS("/component/main.js");
}
```
