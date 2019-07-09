---
title: Jupyterlab的安装与配置
date: 2019-07-02 21:28:22
tags:
- Jupyter
- Python
categories:
- Python教程
---

## 配置Jupyter lab

打开 Terminal，输入以下命令：

```bash
jupyter lab --generate-config
```

这会在 `~/.jupyter/` 目录下生成一个 `jupyter_notebook_config.py` 文件。

<!--more-->

```bash
cd ~/.jupyter
code jupyter_notebook_config.py
```
上面的 code 命令，需要你已经安装 Visual Studio Code，并且在已经在其中设置了 `Install 'code' command in PATH`。

文件内容很长，有空可以仔细看。可以直接将以下内容拷贝粘贴到文件底部，根据需求修改：

```json
#c.NotebookApp.token = ''
#c.NotebookApp.open_browser = False
#c.NotebookApp.notebook_dir = '~/'
#c.NotebookApp.default_url = '/tree'
```

逐条解释一下：

> `c.NotebookApp.token = ''`

每次打开 Jupter，它都会给你生成一个新的 Token —— 这是安全策略。但是，如果你只是在自己的电脑上使用，那么，这就给你制造了麻烦，因为若是你想同时用另外一个浏览器打开它，那你就需要从 Terminal 里拷贝那个 Token 出来。所以，你可以在配置文件里直接把它设置为空。

> `c.NotebookApp.open_browser = False`

每次你执行 `jupyter lab` 或者 `jupyter notebook` 命令的时候，它都会使用系统默认浏览器。

每个人的习惯不一样。比如我，会想到用一个平时不怎么用的浏览器专门用在 Jupyter 上，这样会防止自己在关闭其它网页的时候不小心把 Jupyter 关掉…… 那我就会把这项设定为 `False`。

> `c.NotebookApp.notebook_dir = '~/'`

在 Terminal 中执行 `jupyter` 命令的时候，它默认是在你当前所在的工作目录打开 `jupyter`，这同样是出于安全考虑。但是，如果你只是在自己的电脑上使用，且只有自己在使用，那么莫不如直接把它设置成 `~/`，即，你的用户根目录，这样会很方便地访问各种地方的文件……

> `c.NotebookApp.default_url = '/tree'`

这一项留给那些依然习惯于使用 jupter notebook 的人，这样设置之后，即便是输入 `jupyter lab` 命令，打开的还是 jupyter notebook。

> c.NotebookApp.port = 1234

修改监听端口，防止与常用端口冲突。

在 Terminal 里常用的与 Jupyter 有关的命令有：

```bash
jupyter lab
jupyter lab --version
conda install -c conda-forge jupyterlab # 这是用来升级 jupyter lab 到最新版的方法
jupyter notebook list                   # 查看正在运行的 jupyter lab/notebook
jupyter notebook stop                   # 停止 jupyter lab/notebook 服务
```

## 将Jupyter lab配置成系统服务

如果，你厌烦每次都要跑到 Terminal 里启动 Jupyter lab，可以把它配置成系统服务，每次开机启动它就自动运行。

```bash
code ~/Library/LaunchAgents/com.jupyter.lab.plist
```

这条命令会让 Visual Studio Code 创建 `~/Library/LaunchAgents/com.jupyter.lab.plist`文件并打开。

在其中拷贝粘贴以下内容，注意，要把其中的 `your_username` 修改为你的用户名：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Label</key>
	<string>local.job</string>
	<key>ProgramArguments</key>
	<array>
		<string>/Users/your_username/anaconda3/bin/jupyter</string>
		<string>lab</string>
		<string>--no-browser</string>
		<string>--notebook-dir=/Users/your_username/</string>
	</array>
	<key>RunAtLoad</key>
	<true/>
	<key>StandardErrorPath</key>
	<string>/tmp/local.job.err</string>
	<key>StandardOutPath</key>
	<string>/tmp/local.job.out</string>
</dict>
</plist>
```
如果之前在`jupyter_notebook_config.py` 文件里已经设置过

```json
c.NotebookApp.open_browser = False
c.NotebookApp.notebook_dir = '~/'
```
那么这两行就可以不要了：

```xml
		<string>--no-browser</string>
		<string>--notebook-dir=/Users/your_username/</string>
```


而后在 Terminal 里执行：

```bash
launchctl load ~/Library/LaunchAgents/com.jupyter.lab.plist
```

如果你想重新启动这个服务，那么执行：

```bash
launchctl unload ~/Library/LaunchAgents/com.jupyter.lab.plist
launchctl load ~/Library/LaunchAgents/com.jupyter.lab.plist
```

## 输出所有变量内容

默认情况下，Code Cell 只输出最后一个可以被 evaluate 的值，用 `_` 代表之前刚刚被 evaluate 的值。

于是，为了显示最近 evaluate 的多个值，我们总是不得不使用很多的 `print()`……

如果觉得这事儿比较烦的话，可以在 Cell 最上面写上：

```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"
```

如果还想更省事儿一点，就把这个设置写入配置文件：

```python
c.InteractiveShell.ast_node_interactivity = "all"
```