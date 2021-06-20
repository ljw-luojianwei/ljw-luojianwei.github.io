---
title: Android 源码管理之三 git
date: 2021-06-18 14:57:19
categories: 
tags:
- Android
- Tools
comments: true
---

![img](git/1460000023734707)

### Git  流程图

![img](git/1460000023734708)

- <font color="#dd0000">`Workspace`</font>：工作区
- <font color="#dd0000">`Index/Stage`</font>：暂存区
- <font color="#dd0000">`Repository`</font>：仓库区(或本地仓库)
- <font color="#dd0000">`Remote`</font>：远程仓库

### 配置 Git

```bash
# 配置全局用户
$ git config --global user.name "用户名"
$ git config --global user.email "git账号"
# 配置别名
$ git config --global alias.cf config
$ git config --global alias.co checkout
$ git config --global alias.st status
$ git config --global alias.ci commit
$ git config --global alias.br branch -a -vv
$ git config --global alias.rg reflog
# 这里只是美化 log 的输出，实际使用时可以在 git lg 后面加命令参数，如：git lg -10 显示最近10条提交
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
# 删除全局配置
$ git config --global --unset alias.xxx
$ git config --global --unset user.xxx
```

### 查看 Git 信息

```bash
# 查看系统配置
$ git config --list
# 查看用户配置
$ cat ~/.gitconfig
# 查看当前项目的 git 配置
$ cat .git/config
# 查看暂存区的文件
$ git ls-files
# 查看本地 git 命令历史
$ git reflog
# 查看所有 git 命令
$ git --help -a
# 查看当前 HEAD 指向
$ cat .git/HEAD

# git 中 D 向下翻一行  F 向下翻页  B 向上翻页  Q 退出
# 查看提交历史
$ git log --oneline
          --grep="关键字"
          --graph
          --all
          --author "username"
          --reverse
          -num
          -p
          --before=  1  day/1  week/1  "2019-06-06"
          --after= "2019-06-06"
          --stat
          --abbrev-commit
          --pretty=format:"xxx"
          
# oneline -> 将日志记录一行一行的显示
# grep="关键字" -> 查找日志记录中(commit提交时的注释)与关键字有关的记录
# graph -> 记录图形化显示 ！！！
# all -> 将所有记录都详细的显示出来
# author "username" -> 查找这个作者提交的记录
# reverse -> commit 提交记录顺序翻转
# before -> 查找规定的时间(如:1天/1周)之前的记录
# num -> git log -10 显示最近10次提交 ！！！
# stat -> 显示每次更新的文件修改统计信息，会列出具体文件列表 ！！！
# abbrev-commit -> 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符 ！！！
# pretty=format:"xxx" ->  可以定制要显示的记录格式 ！！！
# p -> 显示每次提交所引入的差异(按 补丁 的格式输出)！！！
```

