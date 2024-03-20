+++
author = "sin1111yi"
title = "Git 常用命令记录"
date = "2024-03-20"
description = "程序员的基本素养。"
tags = [
    "git",
    "virtue"
]
series = ["git"]
+++

熟练使用Git是程序员的美德！
<!--more-->

## Git 常用命令记录

假设一个并不存在的github仓库 `https://github.com/sin1111yi/AnExampleExistRepo`

有分支 `branch.1`、`branch.2`、`branch.3`

以 https 拉取为例，如果使用 ssh 则将链接换成 ssh 链接。

### 克隆

- 带子模块克隆

```bash
git clone --recursive https://github.com/sin1111yi/AnExampleExistRepo.git
```

- 新建本地仓库并拉取远程仓库 

```bash
mkdir AnExampleExistRepo && cd AnExampleExistRepo
git init
git remote add origin https://github.com/sin1111yi/AnExampleExistRepo.git
git pull
```

### 分支

- 本地分支重命名

```bash
git checkout branch.1
git branch -m branch.x
```
