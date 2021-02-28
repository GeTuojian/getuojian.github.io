---
title: Scrapy教程（2）：如何调试
date: 2019-07-09 23:27:11
tags:
- Python
- Scrapy
categories:
- 爬虫教程
---

> 由于scrapy爬虫是在终端下运行的，直接在py文件中设置断点无法调试。
>
> 记录一些调试方法。

<!--more-->

## 调试爬虫

### 方式一

在spider中添加以下代码：

```python
# 命令行调试代码
from scrapy.shell import inspect_response
inspect_response(response, self)
```

然后命令行运行`scrapy crawl 爬虫名`即可进入shell调试状态。

### 方式二

在爬虫项目主目录下新建`run.py`文件：

```python
# -*- coding: utf-8 -*-
from scrapy import cmdline

name = 'sse-report'
cmd = 'scrapy crawl {0}'.format(name)
cmdline.execute(cmd.split())
```

然后在爬虫的任意位置设置断点，运行`run.py`，即可在断点处跟踪。

建议结合vscode的debug功能使用，倍儿爽！

