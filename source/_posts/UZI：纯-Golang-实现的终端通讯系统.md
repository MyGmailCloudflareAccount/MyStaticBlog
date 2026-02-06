---
title: UZI：纯 Golang 实现的终端通讯系统
date: 2026-02-06 05:37:42
---

## 使用方法

首先到 https://github.com/runoneall/uzi/releases 下载最新二进制文件

```plaintext
$ uzi --help
纯 Golang 实现的终端通讯系统

Usage:
  uzi [flags]
  uzi [command]

Available Commands:
  connect     连接后端服务
  help        Help about any command
  serve       启动后端服务

Flags:
      --auth string   认证信息 (default "none")
  -h, --help          help for uzi
      --host string   监听/连接地址 (default "ALL")
  -p, --port string   监听/连接端口 (default "4870")

Use "uzi [command] --help" for more information about a command.
```

## 扩展包使用

uzi 提供了一些扩展包，对实时通讯的一些功能进行了封装，可用于集成到你的项目中

- `uzi/conn` 该包提供了对大量网络连接 `net.Conn` 的 `添加` `删除` `广播` 功能的支持，并进行了算法优化，时间复杂度 `O(1)`
- `uzi/history` 该包提供了对大量字符串历史记录的支持，支持设置最大记录条数，并进行了算法优化，时间复杂度 `O(n)`
- `uzi/protocol` 该包提供了一种自定义传输协议，通过结构化的数据 `8字节类型长度 + 类型字符串 + 8字节数据长度 + 数据字节数组` 进行传输，支持直接在连接 `net.Conn` 中读取和写入

### uzi/conn 详细使用文档

1. 添加新连接

    当新连接创建时，需要调用 `conn.Mgr.Add(<net.Conn>)` 将该连接加入连接池，该方法会返回新增的连接在连接池中的 ID

2. 删除连接

    将连接从连接池中删除，需要调用 `conn.Mgr.Remove(<ID>)`

3. 广播消息

    调用 `conn.Mgr.Broadcast(func(conn net.Conn) bool {})` 批量对连接池中的每一个连接执行相同的操作，操作函数内允许阻塞，操作函数需返回一个布尔值代表操作是否成功完成

    Broadcast 会自动维护连接生命周期，一旦操作函数返回 `false`，Broadcast 会自动从连接池中删除对应的连接

    Broadcast 最大并发量为 100 个 Goroutine

| 方法      | 时间复杂度 (平均) | 空间复杂度 |
| --------- | ----------------- | ---------- |
| Add       | O(1)              | O(1)       |
| Remove    | O(1)              | O(1)       |
| Broadcast | O(n)              | O(n)       |

### uzi/history 详细使用文档

1. 初始化管理器

    调用 `history.InitHistoryMgr(<max>)` 初始化历史记录管理器，该方法需传入数字代表最大记录数量，一旦记录超过这个数量，则会删除最早的数据

2. 添加历史记录

    调用 `history.Add(<string>)` 记录传入的字符串到历史记录

3. 获取全部记录

    调用 `history.Get()` 获取全部记录，该方法返回 `[]string` 代表所有被记录的数据

| 操作 | 时间复杂度 | 空间复杂度 |
| ---- | ---------- | ---------- |
| Add  | O(1)       | O(1)       |
| Get  | O(n)       | O(n)       |

### uzi/protocol 详细使用文档

1. 数据结构

    protocol 使用的数据结构为

    ```go
    type Payload struct {
       MsgType string
       MsgData []byte
    }
    ```

    写入和读取数据都通过 `Payload` 进行

2. 从连接中读取数据

    调用 `protocol.Read(<io.Reader>)` 读取一个 `Payload`，该方法可以传入任何实现了 `io.Reader` 的对象，该方法返回两个值 `(Payload, error)`，`Payload` 代表解码的数据，`error` 代表是否发生错误

3. 写入数据到连接

    调用 `protocol.Write(<io.Writer>, <Payload>)` 写入一个 `Payload`，该方法的第一个参数为任何实现了 `io.Writer` 的对象，第二个参数为实例化的 `Payload` 对象，该方法返回 `error` 代表是否发生错误
