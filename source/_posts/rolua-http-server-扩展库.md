---
title: rolua http server 扩展库
date: 2026-01-22 07:50:42
---

该库旨在演示 rolua 与 golang 的无缝集成

```go
// rohttp-server.go

package rolib

import (
	"net/http"

	"github.com/arnodel/golua/lib/golib"
	rt "github.com/arnodel/golua/runtime"
)

func Init() *rt.Table {
	pkg := rt.NewTable()

	pkg.Set(
		rt.StringValue("NewServeMux"),
		rt.FunctionValue(rt.NewGoFunction(NewServeMux, "NewServeMux", 0, false)),
	)

	pkg.Set(
		rt.StringValue("ListenAndServe"),
		rt.FunctionValue(rt.NewGoFunction(ListenAndServe, "ListenAndServe", 2, false)),
	)

	return pkg
}

func NewServeMux(t *rt.Thread, gc *rt.GoCont) (rt.Cont, error) {
	return gc.PushingNext(
		t.Runtime,
		golib.NewGoValue(
			t.Runtime,
			http.NewServeMux(),
		),
	), nil
}

func ListenAndServe(t *rt.Thread, gc *rt.GoCont) (rt.Cont, error) {
	if err := gc.CheckNArgs(2); err != nil {
		return nil, err
	}

	addr := gc.Arg(0).AsString()
	handler := gc.Arg(1).AsUserData().Value().(*http.ServeMux)

	http.ListenAndServe(addr, handler)

	return gc.Next(), nil
}
```

lua 端调用

```lua
local rolib = require("rolib")
local s = rolib.require("rohttp-server.go")

local mux = s.NewServeMux()

mux.HandleFunc("/", function(w, r)
    w.Write('hi there from Lua! You requested ' .. r.URL.Path .. '\n')
end)

s.ListenAndServe(":10907", mux)
```

基于 go 的 net/http，将其导出给 lua 使用
