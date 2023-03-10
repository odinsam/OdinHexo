title: Hexo Pure主题给Post页面添加keywords
author: OdinSam
tags:
  - Hexo
  - Seo
  - Pure
categories:
  - Hexo
abbrlink: 409f
date: 2021-06-09 08:51:00
---
> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。https://hexo.io/zh-cn/ 这里是他的中文文档，无论是结合 Github、Gitee 或者是在私人的云端都可以快速部署，非常高效快捷。为了更好的seo优化，使用pure主题给每一个post页面添加keywords 关键字。

<!-- more -->

#### 找到主题 pure/layout/_common/head.ejs 文件，在 title 标签后添加以下代码:

```js
<% if (post.tags && post.tags.length) { %> 
	<% var kw = "";post.tags.forEach(t=>{ kw+=t.name+',' });kw = kw.substr(0,kw.length-1) %>
	<meta name="keywords" content="<%= kw %>" />
	<% } else if (config.keywords){ %>
	<meta name="keywords" content="<%= config.keywords %>" />
<%} %>
```

#### 这样每一个页面都可以依据发布时的tag生成keywords标签

```html
<meta name="keywords" content="Hexo,Seo,Pure">
```

#### 如果发布时没有tag，那么会依据hexo的配置文件(<font color="red">注意不是pure的配置文件</font>) _config.yml 中的 keywords 生成对应的 meta 标签

```html
<meta name="keywords" content=".Net Core,javascript,typescript,html5,css,css3,linux,react,vue,js">
```