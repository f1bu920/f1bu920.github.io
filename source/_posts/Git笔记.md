---
title: Git笔记
date: 2019-11-23 17:47:42
categories: Git
tags: Git
---

### 下载与安装Git

[Git下载](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)，在git Bash下执行`git --version`，返回Git版本。

<!--more--> 

### 最小配置

#### 配置user信息

- 配置user.name和user.email：

  > git conﬁg --global  user.name ‘your_name’
  >
  > git conﬁg --global  user.email ‘your_email@domain.com'

#### config的三个作用域

- 缺省默认为local

> git config --local：只对本地仓库生效
>
> git config --global：对登录用户所有仓库生效
>
> git config --system：对系统所有用户生效

- 显示config的配置，加上`--list`

  >  git conﬁg --list --local  
  >
  >  git conﬁg --list --global 
  >
  >  git conﬁg --list --system

- 清除，加上 `--unset`

  > ​git conﬁg --unset --local user.name  
  >
  > git conﬁg --unset --global user.name 
  >
  > git conﬁg --unset --system user.name

- 优先级：local>global>system

### 创建Git仓库

两种方式创建

1. Git之前已有代码

   > cd 文件夹
   >
   > git init

2. Git之前没有项目代码

   > cd 文件夹
   >
   > git init your_project_name
   >
   > cd your_project_name

