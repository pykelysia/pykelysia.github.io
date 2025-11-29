# JWT 学习

JWT(JSON Web Token) 是一种在网络应用开发中用于身份验证和信息交换的开放标准。

(所有代码的运行结果均可以在 GitHub 仓库中查看：[pykelysia/jwt-learn](https://github.com/pykelysia/jwt-learn))

## 组成

包括三部分：

1. Header(头部)：用于表明信息类型（`"typ"`），以及使用的签名算法（`"alg"`，加密算法）。
2. Payload(负载)：主要包含 `"Claim"` （声明）内容。该内容中包含注册声明，公共声明和私有声明。

   - 注册声明：是官方预定义的声明字段，非必须但推荐。

   |Claim|含义|
   |:-------:|:--------:|
   |iss|发行者|
   |sub|主题，通常指用户的唯一标识|
   |aud|观众，表示JWT的接收者|
   |exp|过期时间，表示JWT的过期时间，这个时间必须是将来的|
   |nbf|不可用时间，在此时间之前，JWT不应被接受处理|
   |iat|发行时间|
   |jti|JWT的唯一标识，防止被重放|

   - 公共声明：可以自定义，但为了避免冲突需要在 [IANA JSON Web Token Registry](https://www.iana.org/assignments/jwt/jwt.xhtml) 注册。

   - 私有声明：是双方约定使用的自定义字段，通常用于携带用户的其他信息。

3. Signature(签名)：为了防止数据被篡改，使用编码后的 header 和 payload 以及一个密钥，通过指定的算法生成签名。

## 使用

通过 `golang` 命令安装 `Go JWT` 依赖：

```sh
go get -u giyhub.com/golang-jwt/jwt/v5
```

### 创建JWT

#### `NewWithClaims`

```go
func NewWithClaims(method SigningMethod, claims Claims) *Token
```

- `method`：签名（加密）算法，在 `jwt` 依赖中均有预设枚举，例如 `jwt.SigningMethodHS256`。
- `claims`：即为 JWT 的负载部分，通过 `jwt.MapClaims` 实现。例如：

```go
mapClaims := jwt.MapClaims{
    "iss": "pyke",
    "sub": "pykelysia.github.io",
    "aud": "elysia",
}
```

函数用例：

```go
// https://github.com/pykelysia/jwt-learn/tree/main/NewWithClaims
package main

import (
 "fmt"

 "github.com/golang-jwt/jwt/v5"
)

func main() {
 mapClaims := jwt.MapClaims{
  "iss": "pyke",                // 发行者
  "sub": "pykelysia.github.io", // 接收者
  "aud": "elysia",              // 接收者
 }
 token := jwt.NewWithClaims(jwt.SigningMethodHS256, mapClaims)
 fmt.Println(token)
}
```

#### `New`

```go
func New(method SigningMethod) *Token
```

- `method`：签名（加密）算法，在 `jwt` 依赖中均有预设枚举，例如 `jwt.SigningMethodHS256`。

函数用例：

```go
// https://github.com/pykelysia/jwt-learn/tree/main/New
package main

import (
 "fmt"

 "github.com/golang-jwt/jwt"
)

func main() {
 token := jwt.New(jwt.SigningMethodHS256)
 fmt.Println(token)
}
```

个人认为该函数在实际生产中使用应该不是很多，因为该函数生成的 JWT 没有负载内容，无法携带任何信息。

### 生成 JWT 字符串

#### `SignedString`

`SignedString` 是 `jwt.Token` 的一种方法，用于生成最后签名（加密）后的JWT字符串。

```go
func (t *Token) SignedString(key interface{}) (string, error)
```

使用用例：

```go
// https://github.com/pykelysia/jwt-learn/tree/main/SignedString
package main

import (
 "crypto/rand"
 "fmt"

 "github.com/golang-jwt/jwt/v5"
)

func GenerateRandomKey() ([]byte, error) {

 key := make([]byte, 32)
 if _, err := rand.Read(key); err != nil {
  return nil, err
 }

 return key, nil
}

func main() {

 jwtkey, err := GenerateRandomKey()
 if err != nil {
  panic(err)
 }

 mapClaims := jwt.MapClaims{
  "iss": "pyke",                // 发行者
  "sub": "pykelysia.github.io", // 接收者
  "aud": "elysia",              // 接收者
 }
 token := jwt.NewWithClaims(jwt.SigningMethodHS256, mapClaims)

 tokenString, err := token.SignedString(jwtkey)
 if err != nil {
  panic(err)
 }

 fmt.Println(tokenString)
}
```

### 解析 JWT 字符串

#### `Parse`

```go
func Parse(tokenString string, keyFunc Keyfunc, options ...ParserOption) (*Token, error)
```

- `tokenString`：要解析的 JWT 字符串。
- `keyFunc`：一个回调函数，用于提供用于验证签名的密钥。
- `options`：可选参数，用于配置解析行为。

函数用例：

```go
// https://github.com/pykelysia/jwt-learn/tree/main/Parse
package main

import (
 "crypto/rand"
 "fmt"

 "github.com/golang-jwt/jwt/v5"
)

func GenerateRandomKey() ([]byte, error) {

 key := make([]byte, 32)
 if _, err := rand.Read(key); err != nil {
  return nil, err
 }

 return key, nil
}

func ParseJWT(tokenString string, jwtkey []byte, options ...jwt.ParserOption) (*jwt.Token, error) {
 token, err := jwt.Parse(tokenString,
  func(token *jwt.Token) (interface{}, error) {
   return jwtkey, nil
  }, options...)
 if err != nil {
  return nil, err
 }
 if !token.Valid {
  return nil, fmt.Errorf(("invalid token"))
 }
 return token, nil
}

func main() {

 jwtkey, err := GenerateRandomKey()
 if err != nil {
  panic(err)
 }

 mapClaims := jwt.MapClaims{
  "iss": "pyke",                // 发行者
  "sub": "pykelysia.github.io", // 接收者
  "aud": "elysia",              // 接收者
 }
 token := jwt.NewWithClaims(jwt.SigningMethodHS256, mapClaims)

 tokenString, err := token.SignedString(jwtkey)
 if err != nil {
  panic(err)
 }

 tokenParsed, err := ParseJWT(tokenString, jwtkey)
 if err != nil {
  panic(err)
 }

 fmt.Println(tokenString, "\n\n", tokenParsed, "\n\n", tokenParsed.Claims)
}
```

#### `ParseWithClaims`

```go
func ParseWithClaims(tokenString string, claims, Claims, keyFunc Keyfunc, options ...ParserOption) (*Token, error)
```

- `tokenString`：要解析的 JWT 字符串。
- `claims`：一个实现了 `Claims` 接口的结构体，用于存储解析后的声明。实际上就是将函数的返回值 `*Token` 里的属性 `Claims` 返回给 `claims`。
- `keyFunc`：一个回调函数，用于提供用于验证签名的密钥。
- `options`：可选参数，用于配置解析行为。

函数用例：

```go
// https://github.com/pykelysia/jwt-learn/tree/main/ParseWithClaims
package main

import (
 "crypto/rand"
 "fmt"

 "github.com/golang-jwt/jwt/v5"
)

func GenerateRandomKey() ([]byte, error) {

 key := make([]byte, 32)
 if _, err := rand.Read(key); err != nil {
  return nil, err
 }

 return key, nil
}

func ParseJWTWithClaims(tokenString string, jwtkey []byte, options ...jwt.ParserOption) (jwt.Claims, error) {
 mc := jwt.MapClaims{}
 token, err := jwt.ParseWithClaims(tokenString, mc,
  func(token *jwt.Token) (interface{}, error) {
   return jwtkey, nil
  }, options...)
 if err != nil {
  return nil, err
 }
 if !token.Valid {
  return nil, fmt.Errorf(("invalid token"))
 }
 return mc, nil
}

func main() {

 jwtkey, err := GenerateRandomKey()
 if err != nil {
  panic(err)
 }

 mapClaims := jwt.MapClaims{
  "iss": "pyke",                // 发行者
  "sub": "pykelysia.github.io", // 接收者
  "aud": "elysia",              // 接收者
 }
 token := jwt.NewWithClaims(jwt.SigningMethodHS256, mapClaims)

 tokenString, err := token.SignedString(jwtkey)
 if err != nil {
  panic(err)
 }

 claims, err := ParseJWTWithClaims(tokenString, jwtkey)
 if err != nil {
  panic(err)
 }

 fmt.Println(tokenString, "\n\n", claims)
}
```

## 参考

[Go JWT 全面指南](https://learnku.com/articles/85927)
