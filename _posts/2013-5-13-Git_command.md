---
layout: _post
tagline: git 常用命令记录
category: memo
tags: [git]
---
{% include JB/setup %}
## 比较常用的git命令记录

#### 重命名分支
    git branch -m <old_name> <new_name>

#### 查看远程分支
    git branch -r
    git branch -r | grep <char> // 带关键字的查看

#### 查看最后一次提交和提交的详细信息
    git log -n 1 --stat