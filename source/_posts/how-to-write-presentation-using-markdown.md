title: 如何使用 Markdown 写 PPT?
date: 2018-01-12 01:17:51
tags:
- markdown
---

## 为啥要用 Markdown 去写 PPT?
- 相比 PowerPoint 更轻量, 更灵活
- 能够高亮代码
- 专注文字内容, 节省排版和制作动画的时间

## 用什么工具去生成 PPT?
有以下几个工具可供选择

### [reveal.js](https://github.com/hakimel/reveal.js)

[Hakim El Hattab](https://twitter.com/hakimel) 创造了 reveal.js, 他随后创建了 [Slides](https://slides.com), 这是一个使用 reveal.js 创建幻灯片的网站

### [cleaver](https://github.com/jdan/cleaver)
为 hacker 准备的, 只需写 markdown, 然后用工具生成 html 网页

### [md2googleslides](https://github.com/googlesamples/md2googleslides)
google 推出的一个工具, 需要使用 [Slides API](https://developers.google.com/slides)

### [nodeppt](https://github.com/ksky521/nodeppt)
功能与 reveal.js 类似, 社区活跃度不如 reveal.js



根据简单, 功能丰富, 社区活跃度这几个维度最终选择使用 reveal.js 去生成 PPT

## reveal.js 的现状
有以下几种方式去使用 reveal.js

1. 将 markdown 嵌入到 html 文件中

2. 只写 markdown, 然后使用工具将 markdown 渲染成 html, 代表工具有 [reveal-md](gghttps://github.com/webpro/reveal-md), [Pandoc](https://github.com/jgm/pandoc)

3. 建立 reveal.js 网站, 左边写 markdown, 右边可以实时渲染


### 方案 1 vs 方案 2
由于写 ppt 的过程中需要经常预览, 方案 1 和 2 效果其实差不多

方案 1 不需要安装额外的工具, 将 markdown 嵌套在 html 文件中, 打开浏览器就可以预览了, 实现的方式更简单一些

所以笔者更偏好方案 1

### 方案 1 vs 方案 3
方案 3 体验会最好, 但如果想实现方案 3, 得搭建一个网站, 将 markdown 存到 数据库里, 实现比较复杂


综上, 选择方案 1

## reveal.js 使用总结
### 目录结构
从 GitHub 上下载 reveal.js 压缩包, 解压后内容如下, 重要的目录就这几个, 其他都是一些测试文件或者示例
```plain
|____2017
| |____12-28.html
| |____images
| | |____12-28-1.png
| | |____12-28-1.png
| | |____12-28-1.png
|____README.md
|____template.html
|____reveal.js
| |____css
| |____js
| |____lib
| |____plugin
```

以年份划分目录结构, 图片都放在同一年下面的 `images`目录下, 以`当天日期+序号`的格式命名图片

### 模版文件
template.html, 内容如下

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

    <title>reveal.js</title>

    <link rel="stylesheet" href="../../../css/reveal.css">
    <link rel="stylesheet" href="../../../css/theme/black.css">

    <!-- Theme used for syntax highlighting of code -->
    <link rel="stylesheet" href="../../../lib/css/zenburn.css">

    <!-- Printing and PDF exports -->
    <script>
        var link = document.createElement('link');
        link.rel = 'stylesheet';
        link.type = 'text/css';
        link.href = window.location.search.match(/print-pdf/gi) ? '../../../css/print/pdf.css' : '../../../css/print/paper.css';
        document.getElementsByTagName('head')[0].appendChild(link);
    </script>
</head>
<body>
<div class="reveal">
    <div class="slides">
        <section data-markdown data-separator="^\n---\n$" data-separator-vertical="^\n--\n$">
    <textarea data-template>
		TODO: your markdown here
    </textarea>
        </section>
    </div>
</div>

<script src="../../../lib/js/head.min.js"></script>
<script src="../../../js/reveal.js"></script>

<script>
    // More info about config & dependencies:
    // - https://github.com/hakimel/reveal.js#configuration
    // - https://github.com/hakimel/reveal.js#dependencies
    Reveal.initialize({
        dependencies: [
            {src: '../../../plugin/markdown/marked.js'},
            {src: '../../../plugin/markdown/markdown.js'},
            {src: '../../../plugin/zoom-js/zoom.js', async: true},
            {src: '../../../plugin/notes/notes.js', async: true},
            {
                src: '../../../plugin/highlight/highlight.js', async: true, callback: function () {
                    hljs.initHighlightingOnLoad();
                }
            }
        ]
    });
</script>
</body>
</html>
```

看起来很多东西, 用的时候只需知道在 `<textarea data-template></textarea>`标签中间写 Markdown 内容就好了

这个 html 文件主要就是引用一些 JS, CSS 文件, 负责将 Markdown 渲染成 PPT

### 如何使用?
以年份划分目录结构, 将 template.html 复制到相应的目录下, 可以重命名为`月-日.html`, 在`<textarea data-template></textarea>`标签中间写 Markdown 内容, 写完后保存好, 用浏览器打开该`html`文件查看幻灯片

也可以将这些文件上传到服务器上, 将链接分享给别人, 别人就可以查看你的 PPT 了

### 一些概念
#### 分页
Reveal.js 里页面有两种页面类型，横向的一级页面(slide)、纵向的子页面(fragment)。后者务必嵌套在前者里面。键盘上的左右箭头控制一级页面，上下键移动子页面

- `一级页面`可以认为是不同章节, `子页面`可以认为是同一章节的不同小节
- 用`---`区分`一级页面`, 用`--`区分子页面, 注意这两个符号上下都需要空行

例如
```markdown
Slide 1

---

Slide 2

fragment1

--

fragment2

---

Slide 3
```

#### 元素属性 (Element Attributes)
```markdown
- Item 1 <!-- .element: class="fragment" data-fragment-index="2" -->
- Item 2 <!-- .element: class="fragment" data-fragment-index="1" -->
```
每一行 Markdown 语句, reveal.js 认为是一个元素(element)

上面的语法意思是, 展示一个列表, 按下键盘`右方向键`会先显示 `Item 2`, 再按一次, 才会显示 `Item 1`

我们可以利用元素属性给内容添加一些动画和样式

### 常用技巧
下面列出了一些常用 CSS 技巧, 更多技巧可以查看 [reveal.js 官方文档](https://github.com/hakimel/reveal.js), [reveal.js Demo](https://revealjs.com/#/)

#### 控制图片大小
```markdown
![Alt text](./images/clear_cache.png) <!-- .element: style="width: 500px;" -->
```

#### 左右对齐
左对齐
```markdown
方案一 <!-- .element: style="text-align: left;" -->
```

右对齐
```markdown
方案一 <!-- .element: style="text-align: right;" -->
```

#### 文字颜色
```markdown
你好 <!-- .element: style="color: #ff0000;" -->
```

#### 放大页面
按住 `ALT` 键, 用鼠标点击想要放大的地方, 再点击一次, 会恢复原状

#### 页面跳转
在地址后面加上数字, 例如

`https://revealjs.com/#/2` 会跳到第2页

`https://revealjs.com/#/2/2` 会跳到第2页的第2个子页面

## 参考资料
- [Reveal.js：把你的 Markdown 文稿变成 PPT](https://sspai.com/post/40657)
- [前端大神都用什么做 PPT？](https://www.zhihu.com/question/52896240)
