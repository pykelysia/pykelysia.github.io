# 使用Vue创建博客系统

在这个教程中，我们将学习如何使用Vue.js和最小化依赖创建一个功能完整的博客网站。

## 项目概述

本项目的核心特点：
- **Vue 3** - 现代前端框架
- **最小化依赖** - 仅使用必要的库
- **Markdown支持** - 动态渲染文章内容
- **响应式设计** - 支持移动端和桌面端

## 技术栈

### 核心依赖
- `vue` - 前端框架
- `vue-router` - 路由管理
- `marked` - Markdown解析
- `highlight.js` - 代码高亮

### 项目结构

```
vue-blog/
├── public/
│   ├── articles/          # Markdown文章
│   └── config/
│       └── articles.json  # 文章配置
├── src/
│   ├── components/        # Vue组件
│   ├── views/            # 页面组件
│   ├── router/           # 路由配置
│   └── utils/            # 工具函数
```

## 核心功能实现

### 1. Markdown渲染

我们使用`marked`库来解析Markdown内容：

```javascript
import { marked } from 'marked';
import hljs from 'highlight.js';

// 配置marked选项
marked.setOptions({
  highlight: function(code, lang) {
    const language = hljs.getLanguage(lang) ? lang : 'plaintext';
    return hljs.highlight(code, { language }).value;
  },
  langPrefix: 'hljs language-'
});

export function parseMarkdown(content) {
  return marked(content);
}
```

### 2. 文章配置管理

通过JSON文件管理文章元信息：

```json
{
  "articles": [
    {
      "id": "vue-blog-creation",
      "title": "使用Vue创建博客",
      "summary": "详细介绍...",
      "author": "张三",
      "publishDate": "2024-01-15",
      "tags": ["Vue", "前端"],
      "fileName": "vue-blog-creation.md"
    }
  ]
}
```

### 3. 组件架构

#### BlogHeader组件
负责显示博客标题和导航菜单

#### ArticleList组件
展示文章列表，支持筛选和分页

#### MarkdownRenderer组件
安全地渲染Markdown内容

## 样式设计

### 响应式布局

使用CSS Grid和Flexbox实现响应式设计：

```css
.article-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1.5rem;
  padding: 1rem;
}

@media (max-width: 768px) {
  .article-grid {
    grid-template-columns: 1fr;
    gap: 1rem;
  }
}
```

### 主题色彩

```css
:root {
  --primary-color: #2196F3;
  --secondary-color: #FF9800;
  --background: #FAFAFA;
  --text-primary: #212121;
}
```

## 部署和优化

### 构建优化
- 代码分割减少初始加载时间
- 静态资源压缩
- CDN加速

### SEO优化
- 语义化HTML结构
- 合适的meta标签
- 结构化数据

## 总结

通过这个项目，我们学会了：

1. ✅ Vue 3的组合式API使用
2. ✅ 路由和状态管理
3. ✅ Markdown内容渲染
4. ✅ 响应式设计实现
5. ✅ 项目结构组织

这个博客系统虽然简单，但具备了现代博客的核心功能，并且保持了极低的依赖复杂度。

---

*更新时间：2024年1月15日*
*作者：张三*