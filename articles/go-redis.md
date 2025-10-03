# GoLang-Redis 学习

## 目录

- [安装](#安装)
- [连接与基础操作](#连接与基础操作)
- [发布订阅](#发布订阅)

## 安装

需要安装 `Redis`，直接前往[Redis 官网](https://github.com/redis-windows/redis-windows/releases)下载解压即可。

解压完成后，进入解压后文件的根目录，运行文件 `redis-server.exe` 即可。（需确保 `Redis` 服务器一直运行）

## 连接与基础操作

### 连接
`GoLang` 连接 `Redis` 服务器很简单，在官方库 `"github.com/redis/go-redis/v9"` 下有函数 `NewClient(*Options) *Client` 可以直接连接到服务器。
```go
// https://github.com/pykelysia/go-redis/tree/main/base-opt/link-redis
package main

import (
	"context"
	"fmt"

	"github.com/redis/go-redis/v9"
)

func main() {
    // 上下文信息
	ctx := context.Background()

    // 连接函数， 可以自定义对应的 Redis 服务地址，登入密码，等一系列信息
	client := redis.NewClient(&redis.Options{
		Addr: "localhost:6379",
	})
	defer client.Close()

    // 检验连接是否成功
	pong, err := client.Ping(ctx).Result()
	if err != nil {
		fmt.Println("Error connecting to Redis:", err)
		return
	}
	fmt.Println("Redis PING response:", pong)
}
```
### 基础操作
`Redis` 最基础的一个操作是 `string` 的存储与查询。

通过 `Set(context.Context, string, interface{}, time.Duration) *StatusCmd` 即可在 `Redis` 缓存中存储一个键值。

- `context.Context` 上下文
- `string` 存储的 `key` 值
- `interface{}` 存储的 `value` 值
- `time.Duration` 设置该键值对的存在时间

通过 `Get(context.Context, string) *StringCmd` 从缓存中读取 Key 对应的 value 值。在使用该函数之后还需要用 `Result()` 来直接获取 `value` 值

- `context.Context` 上下文
- `string` 存储的 `key` 值

```go
// https://github.com/pykelysia/go-redis/tree/main/base-opt/string-opt
package main

import (
	"context"
	"fmt"

	"github.com/redis/go-redis/v9"
)

func main() {
	ctx := context.Background()
	client := redis.NewClient(&redis.Options{
		Addr: "localhost:6379",
	})
	defer client.Close()

	client.Set(ctx, "key", "value", 60)
	res, err := client.Get(ctx, "key").Result()
	if err != nil {
		fmt.Println("Error:", err)
		return
	}
	fmt.Println("key: ", res)
}
```

### 其他

除此之外 `Redis` 还有很多功能，可自行前往官网查看。

## 发布订阅
发布订阅功能能够让多台 `Redis` 客户接收端收到多台 `Redis` 客户发送端的信息。

### Publish

通过下述方法即可在特定频道发送一条消息。（channel 位置即为频道名）

```go
// https://github.com/pykelysia/go-redis/tree/main/base-opt/pub
err := client.Publish(ctx, "channel", "Hello,GoLang-Redis!").Err()
if err != nil {
    fmt.Println("Publish err:", err)
    return
}
```

### Subscribe

关于 Subscribe 需要注意的是每个 Subscribe 都会长期占有一个通道，所以务必记得在创建时同步使用 defer 使其及时关闭。

```go
// https://github.com/pykelysia/go-redis/tree/main/base-opt/sub
pubsub := client.Subscribe(ctx, "channel")
defer pubsub.Close()

for {
    msg, err := pubsub.ReceiveMessage(ctx)
    if err != nil {
        fmt.Println("ReceiveMessage Error:", err)
        return
    }
    fmt.Println("Received message from channel:", msg.Channel, "Message:", msg.Payload)
}
```

## 参考

[完整代码](https://github.com/pykelysia/go-redis/tree/main/base-opt/)