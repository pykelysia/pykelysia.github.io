# 利用 `go-zero` 搭建微服务
## 目录
1. [工具安装](#相关工具安装)
2. [中心网关](#中心网关)
3. [微服务](#微服务)
4. [中心网关对接微服务](#中心网关对接微服务)
5. [数据库](#数据库)
6. [微服务中链接数据库](#微服务中链接数据库)
## 相关工具安装
使用 `go-zero` 需要安装以下内容
[Golang](#golang)
[MySQL](#mysql)
[etcd](#etcd)
[proto-gen-go](#proto-gen-go)
[goctl](#goctl)
### Golang
直接在官网下载即可。
### MySQL
也是直接前往官网下载即可，可以使用docker来启动一遍管理。
### etcd
可以前往[Release-etcd(Github)](https://github.com/etcd-io/etcd/releases)下载,并运行即可。
### proto-gen-go
```bash
go install -u github.com/golang/protobuf/protoc-gen-go@v1.3.2
```
### goctl
```bash
go install -u github.com/zeromicro/go-zero/tools/goctl@latest
```
如果 `proto-gen-go` 和 `goctl` 未正常安装可以自行上网查找其他方法安装。
## 中心网关
### 中心网关创建
在项目文件下使用 `go` 命令初始化项目
```bash
go mod init usermanager
```
我们将中心网关集中在 `api` 目录下
```bash
mkdir api
cd ./api
```
### 创建api文档
直接通过 `goctl` 创建 api 文档
```bash
goctl api -o usermanager.api
```
新创立的 `usermanager.api` 文件内有部分预先写好的内容（关于该文件格式的语法可能后续会专门写一篇）
```
syntax = "v1"

info (
	title: // TODO: add title
	desc: // TODO: add description
	author: // your git name
	email: // your git email
)

type request {
	// TODO: add members here and delete this comment
}

type response {
	// TODO: add members here and delete this comment
}

service tmp-api {
	@handler GetUser // TODO: set handler name and delete this comment
	get /users/id/:userId(request) returns(response)

	@handler CreateUser // TODO: set handler name and delete this comment
	post /users/create(request)
}
```
`info` 中的 `author`， `email` 一般是直接系统生成。（如果未直接生成可能是因为 `git` 未保存用户名和邮箱）

我们需要该改动的地方就是在 `type` 和 `service` 两个部份内参考示例修改。

// TODO 如何修改，简单介绍
## 微服务
// TODO
## 中心网关对接微服务
// TODO
## 数据库
// TODO
## 微服务中链接数据库
// TODO