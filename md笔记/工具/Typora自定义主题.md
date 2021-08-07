# Typora自定义主题

## 前置知识

因为我们的`markdown`语法实际上是和`html`标签具有一一对应关系的。

所有Typora本质上只是一个将`markdown`语法转换`html`标签，再将`html`标签转换为可视化内容的**浏览器**。

而主题就是通过一个`css`文件来实现渲染样式的。

所以我们只需要编写一个属于自己的`css`文件就能够自定义一个属于自己的主题啦！

💻 必备的前置知识：==HTML==、==CSS==

📚 还不会的先去学一下再过来吧：https://www.runoob.com/

`markdown`与`HTML`之间一一对应关系如下表：

| 描述     | markdown                    | HTML TAG   |
| -------- | --------------------------- | ---------- |
| 标题     | `# 一级标题` `## 二级标题`… | h1,h2…h6   |
| 粗体     | `**粗体**`                  | b , strong |
| 斜体     | `*斜体*`                    | em, i      |
| 分割线   | `---`                       | hr         |
| 无序列表 | `* 1` `- 1` `+ 1`           | ul         |
| 有序列表 | `1.`                        | ol         |
| 引用     | `>`                         | blockquote |
| 超链接   | `[url](url)`                | a          |
| 图片     | `![img](imgUrl)`            | img        |
| 表格     |                             | table      |
| 代码段   |                             | code       |
| 段落     | 无特殊表示                  | p          |

这样我们就可以根据上表的对应关系来修改`css`当中对应的选择器了。

## 怎样写？

`css`的编辑器，很自然的就选择`VS Code`啦！

### 从零开始吗？

不，虽然你可以新建一个空白的`css`文件，从零开始写起。

但是我不建议这么做，我比较建议选取一个已有的主题，复制它的`css`文件，然后在里面做出自己的修改。

因为这样可以参考已有的选择器，会更加方便编写。

### 从已有开始

当然，你可以在`Typora`官网选择你喜欢的主题进行修改，但是这里我们选择的是`Github`主题进行讲解。

## 具体元素选择器

### 字体：

```css
//这里会去网上加载字体文件
@include-when-export url(https://fonts.loli.net/css?family=Open+Sans:400italic,700italic,700,400&subset=latin,latin-ext);

//声明一种字体类型
/* open-sans-regular - latin-ext_latin */
@font-face {
    font-family: 'Open Sans';
    font-style: normal;
    font-weight: normal;
    //先从本地安装的字体中加载，本地没有才去指定url加载
    src: local('Open Sans Regular'), local('OpenSans-Regular'), url('./github/open-sans-v17-latin-ext_latin-regular.woff2') format('woff2');
    unicode-range: U+0000-00FF, U+0131, U+0152-0153,......;
}
......

body {
    //设置字体，从最左边的开始解析，左边字体解析不了才解析右边的。
    font-family: "Open Sans","Clear Sans", "Helvetica Neue", Helvetica, Arial, 'Segoe UI Emoji', sans-serif;
    color: rgb(51, 51, 51);
    line-height: 1.6;
}
```

### md编辑区:

```css
#write {
    max-width: 860px;
  	margin: 0 auto;
  	padding: 30px;
    padding-bottom: 100px;
}
```

### 标题：

```css
h1 {
    font-size: 2.25em;
    line-height: 1.2;
    border-bottom: 1px solid #eee;
}
h2 {
    font-size: 1.75em;
    line-height: 1.225;
    border-bottom: 1px solid #eee;
}
h3 {
    font-size: 1.5em;
    line-height: 1.43;
}
h4 {
    font-size: 1.25em;
}
h5 {
    font-size: 1em;
}
h6 {
   font-size: 1em;
    color: #777;
}
```

### 列表：

```css
li>ol,
li>ul {
    margin: 0 0;
}
```

### 表格：

```css
table th {
    font-weight: bold;
    border: 1px solid #dfe2e5;
    border-bottom: 0;
    margin: 0;
    padding: 6px 13px;
}
table td {
    border: 1px solid #dfe2e5;
    margin: 0;
    padding: 6px 13px;
}
```

### 代码：

```css
.md-fences,
code,
tt {
    border: 1px solid #e7eaed;
    background-color: #f8f8f8;
    border-radius: 3px;
    padding: 0;
    padding: 2px 4px 0px 4px;
    font-size: 0.9em;
}
code {
    background-color: #f3f4f4;
    padding: 0 2px 0 2px;
}
......
```

### 专注模式：

```css
/** focus mode */
.on-focus-mode blockquote {
    border-left-color: rgba(85, 85, 85, 0.12);
}
```

### 侧边栏：

```css
header, .context-menu, .megamenu-content, footer{
    font-family: "Segoe UI", "Arial", sans-serif;
}

.file-node-content:hover .file-node-icon,
.file-node-content:hover .file-node-open-state{
    visibility: visible;
}

.mac-seamless-mode #typora-sidebar {
    background-color: #fafafa;
    background-color: var(--side-bar-bg-color);
}
```

只要找到了这些元素所对应的选择器，我们就可以修改它的样式了，不管是背景、边框还是字体颜色，都可以修改。

当然，这里列出来的选择器只是`Github`主题的一小部分，也仅仅是`Typora`渲染元素的极小部分。

理论上，只要我们能调试出`Typora`渲染出来的元素，就可以根据标签来编写选择器，从而可以更改对应的样式。

## 如何在Typora上调试？

`Typora`是自带前端调试工具的，所以我们可以直接使用。

1. 在设置里面开启调试模式：

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210806104240.png)

2. 在md编辑区的任意位置`右键`->`检查元素`，就可以打开打开调试界面了。

   ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210806104745.png)

3. 点击上方的红圈的小箭头就可以去编辑区查看各种的元素的具体`html标签`了。

有了标签信息，就可以根据标签名或者标签属性，来设置对应的选择器了。

## 最后

我自己自定义了一个主题，有蓝色和橙色两种风格，中文字体使用了`思源黑体`，英文字体使用了`JetBrains Mono`。

想了解和下载的请移步：https://github.com/AZhu12138/azhu-typora-theme

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210805161631.png)

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210805161606.png)

## 参考文章

- 少数派：https://sspai.com/post/59450
- Typora官网中文教程：https://theme.typora.io/doc/zh/Write-Custom-Theme/

