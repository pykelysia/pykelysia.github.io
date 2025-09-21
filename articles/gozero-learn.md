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
```api
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
#### `type`
`type` 后为自定义的数据类型，语法与 `golang` 相似。
```api
type request{
	ok bool `json:"ok"`
}
```
需要注意的是，api文档中的变量名无需首字母大写，当 `goctl` 根据api文档生成项目时会自动转换为大写。

也可以借助 `()` 来实现批量自定义数据。
```api
type (
	request {
		ok bool
	}
	response {
		ok bool
	}
)
```
#### `service`
`service` 为提供某个服务，如：
```api
service tmp-api {
	@handler GetUser // TODO: set handler name and delete this comment
	get /users/id/:userId(request) returns(response)

	@handler CreateUser // TODO: set handler name and delete this comment
	post /users/create(request)
}
```
即为提供名为 `tamp-api` （一般是和api文档创建时相同的名字）的服务，在服务内通过 `@handler` 引出一个网络请求路由，空格后跟随响应函数的函数名。

换行后由请求方式起头，跟随具体的路由，再指出函数参数数据，以及返回的数据。

大致如下：
```api
@handler FunctionName
requestWay /route(dataname) returns(dataname)
```
### 生成中心网关代码
运用 `goctl` 将api文档生成中心网关的代码。

前文生成的api文档在修改后如下内容：
```api
syntax = "v1"

info (
	title: "usermanager"
	desc: "a user manager system"
	author: "myname"
	email: "myemail"
)

type request {
	username int64 `path:"user"`
}

type response {
	ok bool `json:"ok"`
}

service tmp-api {
	@handler GetUser
	get /users/id/:user(request) returns(response)

	@handler CreateUser
	post /users/create(request)
}
```

随后确保在 `/api` 目录下，运行：
```bash
goctl api go -api usermanager.api -dir .
```
即可生成相对应的微服务中心网关代码。
## 微服务
在该阶段我们需要为通过api文档建立的网络响应函数提供微服务。
### 创建 `proto` 文件
返回根目录，执行下述命令。
```bash

# 创建目录 `rpc` 并在该目录下创建目录 `getuser` `createuser` 两个目录。
mkdir rpc
cd rpc
mkdir getuser
mkdir createuser

# 先进入 getuser 目录
cd getuser

# 通过 goctl 创建 getuser.proto 文件
goctl rpc -o getuser.proto

```
此时在 `/rpc/getuser` 目录下出现了文件 `getuser.proto`。

后续该模块的代码初始化将通过该文件实现。

`createuser` 目录下的对应文件使用相同的方式创建。
### 生成微服务框架
将 proto 中的内容修改。
```proto
// getuser.proto
message Request {
	int64 username = 1;
}

message Response {
	bool ok = 1;
}

service Getuser {
  rpc GetUser(Request) returns(Response);
}
```
```proto
// createuser.proto
message Request {
  int64 username = 1;
}

message Response {
  bool ok = 1;
}

service CreateUser {
  rpc CreateUser(Request) returns (Response);
}
```
然后分别在目录 `/rpc/getuser` 和 `/rpc/createuser` 下运行命令：
```bash
# /rpc/getuser
goctl rpc protoc getuser.proto --go_out=. --go-grpc_out=. --zrpc_out=.

# /rpc/createuser
goctl rpc protoc createuser.proto --go_out=. --go-grpc_out=. --zrpc_out=.
```
完成后记得在 `rpc/getuser/etc/getuser.yaml` 中将监听端口从8080更改为8081或其他闲置端口，避免与 `createuser` 的微服务冲突。
## 中心网关对接微服务
// TODO
## 数据库
// TODO
## 微服务中链接数据库
// TODO