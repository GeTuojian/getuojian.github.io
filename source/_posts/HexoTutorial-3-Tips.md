---
title: Hexo基础教程（3）：使用小Tips
date: 2019-06-19 22:02:39
tags: 
- Hexo
- GitHub
categories: 
- Hexo教程
---

## 保留CNAME文件

如果GitHub Pages配置了自定义的域名，则会在Repository下生成一个`CNAME`的文件，里面包含了配置好的自定义域名。

为了避免每次更新blog时，将CNAME文件删除，需要在`blog_name/source`文件夹下新建一个包含相同内容的`CNAME`文件，然后`hexo g`、`hexo d`进行同步。

<!--more-->

## 保留README.md文件

为了让Repository依然保留一个`README.md`文件，同时不被`hexo`渲染，需要手动在`source`目录下添加一个README.md，并将`_config.yml`中的`skip_render`参数改为：

```yaml
skip_render: README.md
```

然后`hexo g`、`hexo d`进行同步。

## 管理Hexo源文件

> 这里我们选择在blog的仓库下建立新的分支，同时管理source code和生成的页面。

在源文件根目录创建git仓库：

```bash
cd blog-name
git init
```

添加`.gitignore`，剔除一些不必要的文件，主要包含：

```bash
.DS_Store
Thumbs.db
db.json
debug.log
node_modules/
public/
.deploy/
themes/
.deploy_git/
_config.yml
```

添加远程仓库路径：

```bash
git remote add origin git@github.com:GeTuojian/getuojian.github.io.git
```

提交本地文件并推送至远程分支：

```bash
git add *
git commit -m "create source branch"
git push origin master:source
```

最后一句的含义是，将`本地仓库的master分支`推送至`远程origin仓库的source分支`，而远程仓库的master分支已经被用来做hexo的部署（还记得hexo d么？）。

## 启用mathjax

在主题配置`themes/next/_config.yml`中修改：

```yaml
# Math Equations Render Support
math:
  enable: true
  per_page: true	#设置为true,不会在每页渲染，需在post开头声明 mathjax: true
  
  engine: mathjax
  #engine: katex
```

安装渲染器：

```bash
$ npm un hexo-renderer-marked --save
$ npm i hexo-renderer-kramed --save 
```

重新生成静态页面：

```bash
$ hexo clean && hexo g -d
# or hexo clean && hexo s
```

## 引用本地图片

在`hexo`配置文件` _config.yml`中开启：

```yaml
post_asset_folder: true
```

在根目录下执行：

```bash
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

使用`hexo n "xxx"`生成新文章，会在`source`目录下同时生成`xxx.md`文件及**同名文件夹**，将图片放在文件夹中，并使用markdown语法在文章中引用即可，引用路径为`xxx/abc.img`。

## 链接本地文章

直接在md文件中写`[链接这篇文章](doc.md)`是无法正确链接到静态文件的，需要写成：

```markdown
{% post_link doc 显示文字%}
```

## 链接文件资源

想在文档里插入文件附件，并上传至github做备份，使用插入图片的方式插入附件，似乎有问题。根据[官方文档的描述](https://hexo.io/zh-cn/docs/asset-folders.html)，需写成如下形式：

```markdown
{% asset_link test.txt 下载地址 %}
```

