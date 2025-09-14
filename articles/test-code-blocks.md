# 测试代码块渲染

这是一个测试文件，用于验证代码块是否能正确渲染。

## JavaScript 代码块

```javascript
function hello() {
  console.log('Hello, World!');
  const message = 'This is a test';
  return message;
}

// 这是一个注释
const arr = [1, 2, 3, 4, 5];
arr.forEach(item => {
  console.log(item);
});
```

## Python 代码块

```python
def hello_world():
    """这是一个Python函数"""
    message = "Hello, World!"
    print(message)
    return message

# 列表推导式示例
numbers = [1, 2, 3, 4, 5]
squares = [x**2 for x in numbers]
print(squares)
```

## HTML 代码块

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>测试页面</title>
</head>
<body>
    <h1>Hello World</h1>
    <p>这是一个测试段落。</p>
</body>
</html>
```

## CSS 代码块

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f0f0f0;
}

.button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
}

.button:hover {
  background-color: #0056b3;
}
```

## 无语言指定的代码块

```
这是没有指定语言的代码块
应该以纯文本形式显示
function test() {
  return "test";
}
```

## 行内代码

这里有一些行内代码：`console.log('Hello')`、`const x = 5`、`<div class="test">`

测试完成！