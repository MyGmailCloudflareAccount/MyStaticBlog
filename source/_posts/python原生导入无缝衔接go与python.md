---
title: python原生导入无缝衔接go与python
date: 2025-06-24 18:46:53
tags: []
categories: 默认
---

地址：https://github.com/FramerOrg/FramerModules/wiki/Module-Document#gobridge

这是一个给 Framer 写的模块，但可以拆开来用：

```python
import sys
import os
import subprocess
import ctypes
from importlib.abc import MetaPathFinder, Loader
from importlib.machinery import ModuleSpec


class GoLoader(Loader):
    def __init__(self, so_path, fullname):
        self.so_path = so_path
        self.fullname = fullname

    def create_module(self, spec):
        return None

    def exec_module(self, module):
        # load library
        lib = ctypes.cdll.LoadLibrary(self.so_path)

        # dynamic function getter
        def get_lib_attr(name):
            return getattr(lib, name)

        # bind to module
        module.__getattr__ = get_lib_attr


class GoFinder(MetaPathFinder):
    def find_spec(self, fullname, path, target=None):
        # get import module name
        module_name = fullname.split(".")[-1]

        # go file search path
        search_paths = sys.path if path is None else path
        for base_path in search_paths:

            # possible file paths
            py_path = os.path.join(base_path, f"{module_name}.py")
            go_path = os.path.join(base_path, f"{module_name}.go")
            so_path = os.path.join(base_path, f"{module_name}.so")

            # ignore python file
            if os.path.isfile(py_path):
                continue

            # process go file
            if os.path.isfile(go_path):

                # ensure newest compiled
                self._ensure_compiled(go_path, so_path)

                # custom module loader
                loader = GoLoader(so_path, fullname)

                # create module spec
                return ModuleSpec(
                    name=fullname, loader=loader, origin=go_path, is_package=True
                )
        return None

    def _ensure_compiled(self, go_path, so_path):
        # build if so file old
        if not os.path.exists(so_path) or (
            os.path.getmtime(go_path) > os.path.getmtime(so_path)
        ):

            # compile go file
            self._compile_go(go_path, so_path)

    def _compile_go(self, go_path, so_path):
        # build command
        cmd = ["go", "build", "-buildmode=c-shared", "-o", so_path, go_path]

        # run command
        try:
            result = subprocess.run(cmd, capture_output=True, text=True, check=True)
            self.logger(f"Compiled {go_path} -> {so_path}")
            if result.stderr:
                self.logger("Compiler warnings: " + result.stderr)

        # catch errors
        except subprocess.CalledProcessError as e:
            raise ImportError(
                f"Go compilation failed for {go_path}:\n"
                f"Exit code: {e.returncode}\n"
                f"Error: {e.stderr}"
            ) from e

        # if go not installed
        except FileNotFoundError:
            raise ImportError("Go compiler not found. Install from https://golang.org/")

sys.meta_path.insert(0, GoFinder())
```

将其保存为一个 python 文件，然后在项目入口处导入一下就行

首先准备一个叫 `mymodule.go` 的文件，内容如下：

```go
package main

import "C" //必须引入C库

import "fmt"

//加入下面注释代码，表示导出，可以被python调用

//export PrintDll
func PrintDll() {
	fmt.Println("我来自dll")
}

//
//export Sum
func Sum(a int, b int) int {
	return a + b
}

func main() {
	//必须加一个main函数，作为CGO编译的入口，无具体实现代码
}
```

然后在 python 内可以直接用 `import mymodule`
会自动将 go 编译为 so/dylib/dll，智能识别更改

每当 go 代码更改后会重新编译，时间较长，编译后下一次就不会重新编译了

以及用作为 python 包也是可以的，目录结构如下：
![](https://s.rmimg.com/original/2X/1/1630d32b4829c738d16538bcc226b4ba2393696e.png)

可以直接用 `from testModule import mymodule`

理论上任何能编译为共享库的语言都能用这套代码
