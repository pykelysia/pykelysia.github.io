# 浏览器原理

当你打开一个网页发生了什么？

完整流程共分为 8 个核心步骤：

- 用户输入 URL

- DNS 解析 -> 获取 IP 地址

- 建立 TCP 连接 （三次握手...）

- 发送 HTTP 请求

- 服务器处理请求并返回响应

- 浏览器接收响应 -> 构建 DOM 和 CSSOM

- 构建渲染树 -> 布局 -> 绘制

- 执行 JS -> 完成页面渲染

## 建立连接

首先需要用户在浏览器上输入要访问的 URL 。

根据用户输入的 URL，浏览器会解析用户输入的 URL 内容，如果是域名的话会 `DNS服务器` 发送请求获取域名对应的 `IP地址` ，然后建立 TCP 连接。

然后通过三次握手建立 TCP 协议连接。

```sh
客户端                              服务器   
│                                    │ 
│──── SYN (我想连接) ────────────────→│ 
│                                    │
│←─── SYN-ACK (好的，你准备好了吗？) ───│ 
│                                    │
│──── ACK (准备好了，开始吧！) ────────→│ 
│                                    │ 
│========== 连接建立，开始传输 =========
```

成功建立协议之后就开始真正发送 HTTP 请求，并获取响应数据。

### **request**

- 请求行：请求方法，URL，HTTP协议版本。

- 请求头(Header)：以键值对形式存储的一些附加信息。例如浏览器类型、字符编码、认证信息等。

- 请求体(Body)：存放数据请求的参数内容。一般使用的是 JSON 类型的格式。

### **response**

- 状态行：状态码，状态码描述，协议版本。

- 响应头(Header)：与请求头相似，也是以键值对形式存储的一些附加信息。响应头的内容是要与请求头的内容相匹配的。

- 响应体(Body)：即为服务端根据对应的请求要返回给客户端的内容。

## 解析

![#](https://i-blog.csdnimg.cn/blog_migrate/04b0db8ce5591f9796b1ea89e3ce5e78.png)

### `DOM` 树(Document Object Model Tree)

解析 HTML ，构建 DOM 树。

解析过程中，遇到 CSS 就会解析 CSS，遇到 JS 就会执行 JS。同时，为了提高解析的效率，浏览器会启动一个预解析的线程，来下载 HTML 文件中外部的 CSS 和 JS 文件。

解析的过程大致可以表示成这几个步骤：

![#](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95330f43ffcb4dc2b1e4230b9c692de1~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1012&h=90&s=23379&e=png&b=fdf3f1)

例如这样的一段代码块，

```html
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
  </body>
</html>
```

浏览器的处理过程如下：

![#](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0df26245b3ac438c9fe64d4d3cbc8ac7~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=780&h=397&s=139673&e=png&b=fdfdfd)

### `CSSOM` 树(CSS Object Model)

构建 CSSOM 树的过程与 构建DOM 的过程非常相似，当浏览器接收到一段CSS，浏览器首先要做的是识别出Token，然后 构建节点 并生成 CSSOM。简述为：

![#](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/72e6a4643779469a90ed97fc6a467592~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1025&h=99&s=25794&e=png&b=fdf4f2)

这一过程中，CSS匹配HTML元素是一个相当复杂和有性能问题的事情，浏览器得递归CSSOM树，确定每一个节点的样式到底是什么，所以DOM树要小，CSS尽量用id和class，千万不要过度层叠下去。

### 渲染树(Render Tree)

当我们生成 DOM 树和 CSSOM 树以后，就需要将这两棵树组合为 渲染树。

![在这里插入图片描述](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/631562e624ba44cb966326f54193f660~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=689&h=327&s=190457&e=png&b=fcfafa)

### 布局

当渲染树完成之后，就会根据渲染树计算树上节点的实际布局位置。并将元素渲染在用户看到的界面。

## `JS` 的作用

JS 代码是通过标签 `<script>` 嵌入在 HTML 文件中的，所以在解析 HTML 构建 DOM 树的时候也会遇到 JS 代码，此时浏览器会停止构建 DOM 树，转而优先执行 JS 脚本。

因为 JS 脚本是有可能修改 DOM 树的内容的，如果在构建完成后再进行执行的话就需要重新构建 DOM 树，显然这是一件非常消耗浏览器性能的事。

## 参考链接

[浏览器从输入URL到页面渲染加载的过程（浏览器知识体系整理）以 前端领域 的知识为重点，并且本文内容超多，建议先了解主干 - 掘金](https://juejin.cn/post/7316775422187061300?share_token=8d2a0a02-e823-497f-bde1-fafe6633cd99)
