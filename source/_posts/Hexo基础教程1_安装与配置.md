---
title: Hexo基础教程(1):安装与配置
date: 2019-06-19 00:00:21
tags: 
- Hexo
- GitHub
categories: 
- Hexo教程
---

> 本篇主要参考[官方文档](https://hexo.io/zh-cn/docs/)。

## Prerequisite

### 安装software

- 安装[nodejs](https://nodejs.org/en/)
- 安装[Git](https://git-scm.com/)
- 注册[GitHub](https://github.com/)

### 解决`npm`全局安装时的权限问题

通过在根目录创建一个隐藏文件夹，来解决`npm`全局安装包时的权限问题：

1. 创建一个目录，用于全局安装：

   ```bash
   mkdir ~/.npm-global
   ```

2. 配置npm的包安装目录：

   ```bash
    npm config set prefix '~/.npm-global'
   ```

3. 在`~/.bashrc`环境变量中添加该路径：

   ```bash
   export PATH=~/.npm-global/bin:$PATH
   ```

4. 刷新环境变量：

   ```bash
    source ~/.bashrc
   ```

5. 测试全局安装是否成功：

   ```bash
    npm install -g jshint
   ```

## 安装Hexo

使用npm安装Hexo，如果报错权限问题，参考0.2。

```bash
npm install -g hexo-cli
```

`cd`至希望新建blog文件夹的目录：

```bash
hexo init blog_name
cd blog_name
npm install
```

启动hexo：

```
hexo s
```

在浏览器中打开[http://localhost:4000](http://localhost:4000/)，就能看到本地部署完成的页面。

## 关联GitHub

### 新建Repository

1. 登录您的 GitHub 账号，新建名称为`您的用户名.github.io`的仓库。假设我的 GitHub 账号的用户名是 `abc`，那么我就应该新建名称为`abc.github.io`的仓库。
2. `Description`可写可不写，随意。
3. 勾上`Initialize this repository with a README`。
4. 点击`Create Repository`完成创建。

### 修改全局配置文件

[Hexo 官方文档](https://hexo.io/zh-cn/docs/configuration.html)中有对全局配置的详细说明，推荐阅读。

找到`blog_name`文件夹下的`_config.yml`文件，在最后修改`deploy`的相关配置：

```yaml
deploy:
  type: git
  repository: https://github.com/xxx/xxx.github.io.git
  branch: master
```

在git中配置你的用户名和邮箱：

```bash
git config --global user.name "yourname"
git config --global user.email "youremail"
```

然后在`blog_name`根目录下执行命令，生成静态文件：

```bash
hexo g
```

在部署前，安装插件：

```bash
npm install hexo-deployer-git --save
```

进行部署：

```
hexo d
```

OK！赶紧去github上看你的新主页吧！

## 常用命令

```bash
hexo new "postName"			#新建文章
hexo new page "pageName"		#新建页面
hexo g					#生成静态页面至public目录
hexo s					#开启预览访问端口（默认端口4000）
hexo d					#将.deploy目录部署到GitHub
```

```bash
hexo clean		#清空缓存
hexo g			#生成静态页面
hexo d			#远程部署
hexo d -g		#生成 & 部署
hexo s -g		#生成 & 预览
```

生成新的文章：

```bash
hexo new "xxx"
```

文章会生成在`/source/_posts/`下，接下来添加一些基本的头信息：

```markdown
---
title: xxx			//在此处添加你的标题。
date: 2016-10-07 13:38:49	//在此处输入编辑这篇文章的时间。
tags: xxx			//在此处输入这篇文章的标签。
categories: xxx			//在此处输入这篇文章的分类。
---
```

然后生成静态页面，并push至GitHub上就ok啦。

