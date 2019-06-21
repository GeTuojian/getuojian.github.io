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