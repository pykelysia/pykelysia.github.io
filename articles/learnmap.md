# LearnMap

(仅供参考)

## 好用的工具

- [GitHub](https://github.com)代码托管，需要配合Git使用
- [Git](https://git-scm.com/)版本控制
- [runoob（菜鸟教程）](https://www.runoob.com/)基本所有主要语言，主流框架在上面都有教程
- [postman](https://www.postman.com/)用于测试后端api接口，也可找其他替代品
- [Qoder](https://qoder.cn/)一个aiIDE，近期可以免费使用，后续将转为商业付费（登陆账号需要Github或者谷歌邮箱）

## 开发基础

### 前端

- HTML/CSS/JavaScript 基础，至少需要将这三门语言的基础内容学好，最基本的拿出这三个语言的代码能看懂。
- TypeScript 目前前端主流语言，是有类型管理的JavaScript，后续学习框架时主要都是倾向于TypeScript。
- 前端框架：Vue3 或 React（任选其一），推荐Vue3，上手更简单一些。

### 后端

- Java 目前全球市场上最主流的后端语言，有深刻的对象管理思想，适合大型项目开发。（但是我没有了解过，不过大二的小学期有Java的课程）。
- Python 语法简单，入门容易，适合初学者，有轻量化的框架 Flask ，有比较大型的框架 Django 。
- Golang 语法简单，性能好，适合微服务开发，近几年发展很快，正在挤占 Java 的市场占比，个人比较推荐。
- JavaScript 也可以用于后端开发，主要是通过 Node.js 运行环境来实现的，但是不怎么主流。

## 我推荐的后端学习路线

1. Golang 基础语法
2. Gin 框架：一种简单便捷的web框架，一般会和 Gorm（ORM框架）一起使用
3. Gorm 框架：对象关系映射框架（可以看作是数据库框架），简化数据库操作

    （上面两个框架使用可以参考我在GitHub上的仓库 [notebook](https://github.com/pykelysia/notebook)）

4. MySQL 数据库：目前最主流的关系型数据库（如果不安装 MySQL 数据库的话，Gorm 框架的使用会有点难受）
5. Redis 数据库：目前最主流的非关系型数据库（简单的小型项目不会涉及，主要是利用缓存来加快数据的读写操作响应）
可以参考[Blog](https://dinglz.cn/tags/redis/)
6. Docker 容器化：目前主流的容器化技术，方便项目的部署和发布
7. Kubernetes 容器编排：目前主流的容器编排技术，方便对多个容器进行管理和编排

    （上面两个其实我也没搞，因为比较麻烦）

8. go-zero 框架：目前比较流行的微服务框架，适合中大型项目开发使用,参考[Blog](https://dinglz.cn/p/golang%E5%BE%AE%E6%9C%8D%E5%8A%A1%E5%AE%9E%E6%88%98/)，注意如果参考这篇博客的话一定要看我根据这篇博客写的项目仓库[gozero-exo](https://github.com/pykelysia/gozero-experience)

## 最后

学完上面这些基本就没什么了，后续就要看你的兴趣方向和项目需求了。而且[后端学习路线](#我推荐的后端学习路线)只要学完第三点就可以去写一下项目了，可以把自己生活中的一些想法实现出来，写的时候可能会有一些技术需要，就可以边写边学。然后在慢慢接触4和5两个和数据处理相关的内容，6和7的话已经涉及到比较高深的容器化技术了，8的话是比较新的微服务框架，用的时候感觉比较好用，顺序可以放在第六点之前。
