---
title: 一个简单的Go语言轮子
date: 2024-12-09 12:43:00
tags: []
categories: 默认
---

此为我自己在写项目时用到的轮子，整理了一下
地址：https://github.com/runoneall/goutil
实现：命令行参数，配置文件，日期，数据库，网络请求等相关功能，简化开发

## command.go

- `Command_Args()` 返回类型 `map[string]string` 参数字典
- `Command_GetArg(arg string)` 返回类型 `string` 参数 `arg` 的值，如果不存在返回空字符串

## config.go

- `Config_Read_File(filepath string)` 返回类型 `map[string]interface{}` 从文件中获取配置，返回配置字典
- `Config_Write_File(filepath string, configMap map[string]interface{})` 无返回 将配置字典写入文件
- `Config_Format(content string, targetFormat string)` 返回类型 `map[string]interface{}` 从字符串中解析配置，需指定配置类型 `targetFormat`，返回配置字典

## date.go

- `Date_Format(formatQuery string, useLong bool, useAlias bool)` 返回类型 `string` 解析日期字符串 `formatQuery`; 使用 `{{year}}` 作为年, `{{month}}` 作为月, `{{day}}` 作为日, `{{hour}}` 作为时, `{{minute}}` 作为分, `{{second}}` 作为秒, `{{week}}` 作为星期; 设置 `useLong` 为 `true` 自动补零, 设置 `useAlias` 为 `true` 使用月和星期的英文名

## mysql.go

- `Mysql_MakeDSN(host string, port int, user string, password string, dbname string)` 返回类型 `string` 制作 DSN 字符串
- `Mysql_Connect(dsn string)` 返回类型 `*sql.DB` 使用 DSN 连接数据库，返回数据库连接对象
- `Mysql_Exec(db *sql.DB, sql string)` 返回类型 `sql.Result` 运行 SQL 语句，返回结果对象
- `Mysql_Query(db *sql.DB, sql string)` 返回类型 `[]map[string]interface{}` 使用 SQL 语句查询数据库，返回结果字典
- `Mysql_Close(db *sql.DB)` 无返回 关闭数据库连接

## network.go

- `Network_Get(url string, headers ...map[string]string)` 返回类型 `*http.Response` 发送 GET 请求到 `url`，可选请求头 `headers`
- `Network_Post(url string, data any, dataType string, headers ...map[string]string)` 返回类型 `*http.Response` 发送 POST 请求到 `url`， `data` 为 请求体， `dataType` 为请求体类型, 可选请求头 `headers`; 当 `dataType` 为 `text` data 必须为 `string`, 当 `dataType` 为 `binary` data 必须为 `[]byte`, 当 `dataType` 为 `json` data 必须为 `map[string]interface{}`, 当 `dataType` 为 `form-kv` data 必须为 `map[string]string`, 当 `dataType` 为 `form-file` data 必须为 `map[string]string` 格式 `键名:文件路径`

## gobr script

- 自己的一个简单的shell脚本，自动编译go文件并运行，支持参数传递
- 使用: `gobr <build name> [sub args]`
- `<build name>` 为要编译的go文件名，不包含后缀 `.go`
- `[sub args]` 为传递给go文件的参数
