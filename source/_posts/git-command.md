---
title: git常用命令
date: 2019-06-19 22:15:19
tags:
- git
- command
categories:
- git教程
---

## git使用基本配置

### 全局设置

```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

<!--more-->

### 设置VSCode作为difftool

1. 命令行运行

   ```bash
   git config --global core.editor "code --wait"
   ```

2. 现在可以在VSCode中配置git

   ```bash
   git config --global -e
   ```

3. 在配置文件中添加：

   ```bash
   [diff]
       tool = default-difftool
   [difftool "default-difftool"]
       cmd = code --wait --diff $LOCAL $REMOTE
   ```

## 本地仓库基本命令

### 创建git仓库

```bash
git init
```

### 查看工作区状态

```bash
git status
git status -s
git status --short
```

### 跟踪/添加/暂存文件

```bash
git add filename
git add *
```

### 提交文件并添加修改说明

```bash
git commit -m "text note"

#使用编辑器提交文件（默认会将最后一次 git status返回的内容，以注释的形式加入）
git commit

#使用编辑器提交文件（默认会将最后一次 git status、git diff的返回内容，以注释的形式加入）
git commit -v
```

### 比较文件

```bash
#比较修改但未暂存的文件
git diff
git difftool

#比较已暂存但未提交的文件
git diff --cached
git diff --staged
git difftool --cached
git difftool --staged

#跳过暂存的步骤，直接commit修改后的文件（如果有新增的文件，首次添加入库仍然需要通过git add命令）
git commit -a
```

### 移除文件

```bash
#先手动删除文件
rm filename
#再使用git rm
git rm filename
#最后提交修改
git commit -m "delete filename"
```

### 修改文件名

```bash
git mv filename_from filename_to
#本质相当于执行了以下三步:
mv filename_from filename_to
git rm filename_fro
git add filename_to
```

### 查看提交历史

```bash
git log

#查看每次提交的内容差异，显示最近两次提交
git log -p -2 

#查看每次提交的相关统计信息
git log --stat

#设定输出的形式
git log --oneline
git log --pretty=oneline
git log --pretty=short
git log --pretty=full
git log --pretty=fuller

#常用选项
-p 显示每次提交前后的差异
--stat 显示文件修改统计信息
--shortstat 显示--stat中最后的行数修改信息
--name-only 仅显示被修改的文件清单
--name-status 显示新增、修改、删除的文件清单
--abbrev-commit 仅显示commit哈希值的前几个字符
--relative-date 使用相对时间显示（几天前、几小时前）
--graph 显示ASCII图形表示的分支合并历史
--pretty 使用其他格式显示历史提交信息

#限制输出长度
-2 -(n) 输出最近n条提交记录
--after --since  仅显示指定时间之后的提交
--since=2.weeks
--since="2008-01-15"
--since="2 years 1 day 3 minutes ago"
--until --before 仅显示指定时间之前的提交
--author 指定作者
--committer 指定提交者
--grep 包含关键字
-Sfunction_name  -S  仅显示添加或移除了指定关键字的提交
--all-match 多个条件同时满足才筛选（与），否则是（或）

#定制输出格式
git log --pretty=format:"%h - %an, %ar : %s"
#常用的输出格式
%H 提交的完整哈希值
%h 提交的简短哈希值
%T 树对象的完整哈希值
%t 树对象的简短哈希值
%P 父对象的完整哈希值
%p 父对象的简短哈希值
%an 作者的名字
%ae 作者的email
%ad 作者修订日期（可以用--date=选项定制格式）
%ar 作者修订日期，按多久以前的方式显示
%cn 提交者的名字
%ce 提交者的email
%cd 提交日期
%cr 提交日期，按多久以前的方式显示
%s 提交说明

#图形化展示分支、合并历史：
git log --pretty=format:"%h %s" --graph
```

### 版本退回

```bash
git reset —-hard HEAD^
git reset —-hard HEAD^^
git reset —-hard HEAD~5
```

### 版本前进

```bash
git reflog
git reset —-hard commitid
```

### 撤销操作

```bash
git commit --amend 
```

- 如果没有任何修改（暂存区没有文件），则仅修改上次的提交信息（说明文字）
- 如果有修改、有暂存，则将此次暂存文件和上一次提交的快照合并，并弹出修改说明信息的窗口

### 取消暂存的文件

如果文件已经通过git add添加进暂存区，但是又想撤销，另起一个commit，可以通过：

```bash
git reset HEAD #撤销前一次所有add文件
git reset HEAD filename #仅撤销某一个文件的暂存状态
```

进行撤销（恢复为修改但未add、未暂存的状态）

### 撤销对文件的修改

放弃对工作区文件的修改，恢复到修改之前状态（但是对修改并且已经git add过、已加入暂存区的文件无效）：

```bash
git checkout -- filename #同样适用于磁盘文件被删除，但想恢复为最近一次提交版本
```

如果想放弃add并放弃修改，依次执行：

```bash
git reset HEAD filename
git checkout -- filename
```

### 追回某次commit中某个文件

如果误删文件后，想追回，且想追回曾经某次commit的版本（如果只想追回最新版本，执行后两句即可）：

```bash
git reset commitid  test.txt
git checkout —- test.txt
git commit -m “track back only one deleted file”
```

### 忽略文件

在写项目的时候，经常会编译自动产生一些不需要放入仓库的文件，例如python编译时产生的`*.pyc`文件、`__pycache__`文件夹等。
可以通过在根目录新建`.gitignore`文件，指定需要忽略的文件或文件夹。   
在.gitignore文件当中，一行代表一条忽略规则，如果是一个带“.”这种有后缀的字符串那么git就会忽略这个文件。“*”表示的就是选中所有，如果没有“.”就表示一个文件夹。
Github给我们提供了预设的[模板](https://github.com/github/gitignore)，如果嫌麻烦就直接下载后丢入项目文件夹，更名为`.gitignore`，git就不会再追踪相关文件。



## 远程仓库基本命令

### 查看远程仓库

```bash
#克隆远程仓库
git clone git@github.com:GeTuojian/the-craft-of-selfteaching.git
#克隆指定分支
git clone -b dev git@github.com:GeTuojian/the-craft-of-selfteaching.git
#查看远程仓库的路径
git remote -v
origin git@github.com:GeTuojian/the-craft-of-selfteaching.git (fetch)
origin git@github.com:GeTuojian/the-craft-of-selfteaching.git (push)
```

### 添加远程仓库

```bash
git remote add pb URL
git remote -v
```

### 从本地push至远程仓库

```bash
#关联github及第一次推送master分支的所有内容
git remote add origin https://github.com/GeTuojian/MyGit.git   
#本地master与远程master关联，fetch关系
git push -u origin master
```

第二句的含义是，在`push`的同时设定分支间的跟踪关系`-u`，此处为本地的`master`关联`origin/master`。

### 从远程仓库中拉取或抓取

```bash
git fetch [remote-name]
git pull
```

### 推送到远程仓库

```bash
git push [remote-name] [branch-name]
```

### 查看远程仓库及跟踪关系

```bash
git remote show origin
```

### 设置分支的跟踪关系

```bash
#新建时设置
git checkout -b new_branch_name  [--track] origin/remote_branch_name
#设置已有分支和远程分支的跟踪关系
git branch -u origin/source master
#push时设置跟踪关系，本地master关联远程origin/source
git push -u origin master:source
```

### 远程仓库的移除和重命名

```bash
git remote rename pb paul
git remote rm paul
```

### 本地分支推送到远程其他分支

```bash
git push <remote> <local branch name>:<remote branch to push into>
```

## 标签 Tags

### 列出标签

```bash
git tag
git tag -l 'V1.8.5*'
```





