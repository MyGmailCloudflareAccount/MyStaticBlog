---
title: 在Typecho中加入自定义的友链功能
date: 2025-01-04 14:04:43
tags: []
categories: 默认
---

首先在主题的 `functions.php` 中新加一个多行输入框
![](https://s.rmimg.com/2025-01-04/1735994677-384654-2025-01-04-204227.png)
名称随意
然后就可以用 `$this->options->你的自定义名称` 获取内容或 `$this->options->你的自定义名称()` 直接输出
例如我是用的json
![](https://s.rmimg.com/2025-01-04/1735995064-865538-image.png)

然后新建一个独立页面，比如links，或者其他名称
建立一个插入点，比如 `<div id="linkItemsArea"></div>`
![](https://s.rmimg.com/2025-01-04/1735994885-489446-image.png)

前端页面处理
![](https://s.rmimg.com/2025-01-04/1735995491-925942-2025-01-04-205142.png)
这样一个自定义友链就写好了，可以增加一点代码，使其更好看

我的js代码

```javascript
let linkItemsHTML = "";
for (const JsonItem of JSON.parse(`<?php $this->options->siteOutLinks() ?>`)) {
    linkItemsHTML += `<hr class="link-separator"><div class="link-item"><img class="link-item-avatar" src="${JsonItem.avatar}"><div class="link-item-content"><a href="${JsonItem.url}" target="_blank"><h2>${JsonItem.title}</h2></a><p>${JsonItem.description}</p></div></div>`;
}
document.getElementById("linkItemsArea").innerHTML = linkItemsHTML;
```
