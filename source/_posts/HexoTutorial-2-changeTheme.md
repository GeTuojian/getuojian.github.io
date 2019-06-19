---
title: Hexo基础教程（2）：修改主题
date: 2019-06-19 21:58:19
tags: 
- Hexo
- GitHub
categories: 
- Hexo教程
---

> 此处我们希望安装[NexT主题](https://theme-next.org/)。
>
> 这里是[中文说明](https://theme-next.iissnan.com/getting-started.html)，这里是[英文说明](https://theme-next.org/docs/getting-started/)。

## 安装

```bash
cd blog_name
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

## 启用主题

修改`_config.yml`中的`theme`字段：

```yaml
theme: next
```

清缓存，验证是否生效：

```bash
hexo clean
hexo s
```

## 主题设定

### 选择Scheme

可以在[GitHub](https://github.com/theme-next/hexo-theme-next)上进行预览。

修改`themes/next`下的`_config.yml`文件，修改Scheme：

```yaml
scheme: Gemini
```

### 设定基本信息

修改站点配置文件中的`site`部分：

```yaml
title: Sturm und Drang
subtitle: 三天三夜，三更半夜
description:
keywords:
author: Ge Tuojian
language: zh-CN
timezone: Asia/Shanghai
```

### 添加主题分类页

新建页面

```bash
 hexo new page categories
```

在新的页面中修改头信息：

```markdown
 title: 文章分类
 date: 2014-12-22 12:39:04
 type: "categories"
 comments: false
 ---
```

编辑主题的 `_config.yml` ，将 `menu` 中的 `categories: /categories` 注释去掉，如下:

```yaml
menu:
  home: / || home
  #about: /about/ || user
  #tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

### 添加标签分类页

新建页面

```bash
 hexo new page tags
```

在新的页面中修改头信息：

```markdown
 title: 文章标签
 date: 2014-12-22 12:39:04
 type: "tags"
 comments: false
 ---
```

编辑主题的 `_config.yml` ，将 `menu` 中的 `tags: /tags` 注释去掉，如下:

```yaml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

####  