---
title: python通用跨语言导入器
date: 2025-06-25 16:47:32
tags: []
categories: 默认
---

本质就是打包成so，然后用CDLL加载，但是可以直接用import

文档：https://github.com/FramerOrg/CrossLanguage/wiki/Module-Document

核心代码就是 `CrossLangImporter`，用Require返回ModuleSpec，封装了自动查找，运行编译，导入钩子

可以自定义后缀处理，当然是py文件优先

具体看如何添加处理器

```python
class moduleMain:
    def __init__(self, framer, logger):
        self.framer = framer
        self.logger = logger

        self.extension = "go"
        self.not_found_message = (
            "Go compiler not found. Install Go from https://golang.org/"
        )

    def build_command(self, file_path, so_path):
        return [
            ["go", "mod", "tidy"],
            ["go", "build", "-buildmode=c-shared", "-o", so_path, file_path],
        ]
```

其中moduleMain及init参数是framer固定的，不用管
主要实现：

1. self.extension：声明该处理器能处理的文件
2. self.not_found_message：若找不到编译器，抛出的错误内容
3. build_command：返回一系列编译命令，其中file_path和so_path是固定的，编译时会进入file_path所在目录

觉得不错就给Framer项目点个Star吧：https://github.com/FramerOrg/Framer
