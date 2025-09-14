# CSS布局完全指南

从基础到高级，全面掌握CSS布局技术，构建现代化的网页界面。

## 布局基础

### 盒模型 (Box Model)

理解盒模型是掌握CSS布局的第一步：

```css
.box {
  width: 300px;
  height: 200px;
  padding: 20px;
  border: 2px solid #333;
  margin: 10px;
  
  /* 标准盒模型：总宽度 = 300 + 20*2 + 2*2 + 10*2 = 364px */
}

.border-box {
  box-sizing: border-box;
  width: 300px;
  padding: 20px;
  border: 2px solid #333;
  
  /* border-box：总宽度 = 300px (padding和border包含在内) */
}
```

### 文档流和定位

```css
/* 静态定位（默认） */
.static {
  position: static;
}

/* 相对定位 */
.relative {
  position: relative;
  top: 10px;
  left: 20px;
}

/* 绝对定位 */
.absolute {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

/* 固定定位 */
.fixed {
  position: fixed;
  top: 0;
  right: 0;
  z-index: 1000;
}

/* 粘性定位 */
.sticky {
  position: sticky;
  top: 20px;
}
```

## Flexbox布局

Flexbox是一维布局的理想选择：

### 基础用法

```css
.flex-container {
  display: flex;
  
  /* 主轴方向 */
  flex-direction: row; /* row | row-reverse | column | column-reverse */
  
  /* 主轴对齐 */
  justify-content: center; /* flex-start | flex-end | center | space-between | space-around | space-evenly */
  
  /* 交叉轴对齐 */
  align-items: center; /* flex-start | flex-end | center | stretch | baseline */
  
  /* 换行 */
  flex-wrap: wrap; /* nowrap | wrap | wrap-reverse */
  
  /* 间距 */
  gap: 1rem;
}

.flex-item {
  /* 弹性增长 */
  flex-grow: 1;
  
  /* 弹性收缩 */
  flex-shrink: 1;
  
  /* 基础大小 */
  flex-basis: auto;
  
  /* 简写 */
  flex: 1 1 auto; /* grow shrink basis */
}
```

### 实际应用案例

#### 水平垂直居中

```css
.center-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

#### 响应式导航栏

```css
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
}

.nav-links {
  display: flex;
  gap: 2rem;
  list-style: none;
}

@media (max-width: 768px) {
  .navbar {
    flex-direction: column;
    gap: 1rem;
  }
}
```

#### 卡片布局

```css
.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  padding: 1rem;
}

.card {
  flex: 1 1 300px; /* 最小宽度300px，可增长 */
  min-height: 200px;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}
```

## CSS Grid布局

Grid是二维布局的最佳选择：

### 基础概念

```css
.grid-container {
  display: grid;
  
  /* 定义列 */
  grid-template-columns: 1fr 2fr 1fr; /* 或 repeat(3, 1fr) */
  
  /* 定义行 */
  grid-template-rows: 100px auto 50px;
  
  /* 间距 */
  gap: 20px; /* 或 grid-gap */
  
  /* 对齐 */
  justify-items: center;
  align-items: center;
}

.grid-item {
  /* 跨列 */
  grid-column: 1 / 3; /* 从第1列到第3列 */
  
  /* 跨行 */
  grid-row: 2 / 4;
  
  /* 简写 */
  grid-area: 2 / 1 / 4 / 3; /* row-start / col-start / row-end / col-end */
}
```

### 命名网格线

```css
.grid-layout {
  display: grid;
  grid-template-columns: [sidebar-start] 250px [sidebar-end main-start] 1fr [main-end];
  grid-template-rows: [header-start] 60px [header-end content-start] 1fr [content-end footer-start] 40px [footer-end];
}

.header {
  grid-column: sidebar-start / main-end;
  grid-row: header-start / header-end;
}

.sidebar {
  grid-column: sidebar-start / sidebar-end;
  grid-row: content-start / content-end;
}

.main {
  grid-column: main-start / main-end;
  grid-row: content-start / content-end;
}
```

### 网格模板区域

```css
.page-layout {
  display: grid;
  height: 100vh;
  grid-template-areas: 
    "header header header"
    "sidebar main aside"
    "footer footer footer";
  grid-template-columns: 200px 1fr 150px;
  grid-template-rows: 60px 1fr 40px;
  gap: 10px;
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main { grid-area: main; }
.aside { grid-area: aside; }
.footer { grid-area: footer; }
```

## 响应式布局技巧

### 移动优先设计

```css
/* 移动端基础样式 */
.container {
  padding: 1rem;
}

.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

/* 平板端 */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
  }
  
  .grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

/* 桌面端 */
@media (min-width: 1024px) {
  .container {
    max-width: 1200px;
    margin: 0 auto;
  }
  
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

### 响应式单位

```css
.responsive-typography {
  /* 视口单位 */
  font-size: 4vw; /* 视口宽度的4% */
  
  /* clamp函数：最小值 首选值 最大值 */
  font-size: clamp(1rem, 4vw, 2.5rem);
  
  /* calc函数 */
  width: calc(100% - 2rem);
  
  /* 容器查询单位 */
  padding: 5cqw; /* 容器宽度的5% */
}
```

### 现代CSS特性

#### 容器查询

```css
.card-container {
  container-type: inline-size;
}

@container (min-width: 300px) {
  .card {
    display: flex;
    flex-direction: row;
  }
}

@container (min-width: 500px) {
  .card {
    grid-template-columns: 1fr 2fr;
  }
}
```

#### CSS子网格

```css
.grid-parent {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1rem;
}

.grid-child {
  display: grid;
  grid-column: span 2;
  grid-template-columns: subgrid; /* 继承父网格的列定义 */
}
```

## 实战案例

### 响应式卡片网格

```css
.article-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  padding: 2rem;
}

.article-card {
  display: grid;
  grid-template-rows: auto 1fr auto;
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.1);
  overflow: hidden;
  transition: transform 0.3s ease;
}

.article-card:hover {
  transform: translateY(-4px);
}

.card-image {
  aspect-ratio: 16/9;
  object-fit: cover;
}

.card-content {
  padding: 1.5rem;
}

.card-footer {
  padding: 1rem 1.5rem;
  border-top: 1px solid #eee;
}
```

### 圣杯布局（Holy Grail）

```css
.holy-grail {
  display: grid;
  min-height: 100vh;
  grid-template-areas: 
    "header"
    "main"
    "footer";
  grid-template-rows: auto 1fr auto;
}

@media (min-width: 768px) {
  .holy-grail {
    grid-template-areas: 
      "header header header"
      "sidebar main aside"
      "footer footer footer";
    grid-template-columns: 200px 1fr 150px;
  }
}
```

## 总结

现代CSS布局技术让我们能够：

- ✅ 使用Flexbox处理一维布局
- ✅ 使用Grid处理二维布局
- ✅ 实现真正的响应式设计
- ✅ 构建复杂而灵活的界面
- ✅ 减少对JavaScript的依赖

掌握这些布局技术，你将能够应对任何复杂的页面布局需求。

---

*更新时间：2024年1月5日*
*作者：王五*