---
title: 纯 Go 实现的 Lua5.4 编译器/运行时
date: 2026-01-22 07:47:43
---

项目地址：https://github.com/rolua-org/rolua

组织地址：https://github.com/rolua-org

本项目基于 https://github.com/arnodel/golua 进行开发

# 编写 rolib 扩展

以下是一个示例代码，很适合开始

```go
// lib1.go
// 此处函数编写与 arnodel/golua 中大体一致

// 必须标明 package 为 rolib
package rolib

import rt "github.com/arnodel/golua/runtime"

func add(t *rt.Thread, gc *rt.GoCont) (rt.Cont, error) {
	num1, err := gc.IntArg(0)
	if err != nil {
		return nil, err
	}

	num2, err := gc.IntArg(1)
	if err != nil {
		return nil, err
	}

	return gc.PushingNext(t.Runtime, rt.IntValue(num1+num2)), nil
}

// 导出函数（必须），返回模块的 table（pkg）
func Init() *rt.Table {
	pkg := rt.NewTable()

	// 添加一些属性
	pkg.Set(rt.StringValue("hello"), rt.StringValue("world"))
	pkg.Set(rt.StringValue("add"), rt.FunctionValue(rt.NewGoFunction(add, "add", 2, false)))

	return pkg
}
```

```lua
-- main.lua

-- 标准库，引入 go 文件导入支持
local rolib = require("rolib")

-- 路径导入需要的 go 文件（单文件，不支持模块）
local lib1 = rolib.require("./lib1.go")

-- 打印 hello 属性
print(lib1.hello) -- 此处应输出 world

-- 计算和（调用 add）
local result = lib1.add(5, 6)
print(result) -- 此处应输出 11
```

输出

```plaintext
world
11
```

lib 中可以使用全部的 go 标准库，具体可看：https://github.com/traefik/yaegi/tree/master/stdlib

# 打包

基于 https://github.com/rolua-org/ropacker 进行跨平台打包
