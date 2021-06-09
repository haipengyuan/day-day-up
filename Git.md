# Git使用

[toc]

## 初始设置

### 设置姓名和邮箱地址

```git
$ git config --global user.name "Your name"
$ git config --global user.email "email@example.com"
```

设置的信息保存在 `～/.gitconfig`  文件中。

```
[user]
  name = Your name
  email = email@example.com
```

这里设置的姓名和邮箱地址会用在Git的提交日志中。

### 提高命令输出的可读性

将 `color.ui` 设置为 `auto` 可以让命令的输出拥有更高的可读性。

```
$ git config --global color.ui auto
```

设置的信息同样也保存在 `～/.gitconfig`  文件中。

```
[color]
  ui = auto
```

## 创建版本库

```git
$ git init
Initialized empty Git repository in G:/Code/git/.git/
```

## 常用操作
```git
$ git add Unity.md
$ git commit -m "create file"
```
可以使用多次add添加不同的文件，再使用一次commit全部提交

```git
$ git status
```
`git status`可以查看仓库当前的状态

```git
$ git diff Unity.md
```
`git diff`可以查看上次对文件做出的修改

## 版本回退
```git
$ git log
commit dd6784debce53d2e142b39ddec32f6327e69195a (HEAD -> master)
Author: 
Date:   Sat Sep 12 15:29:55 2020 +0800

    create Unity.md file

$ git log --pretty=oneline
```
`git log`查看提交日志，`--pretty=oneline`可以简化输出信息。

```git
$ git reset --hard HEAD^

$ git reflog
$ git reset --hard 1094a
```
回退到上一个版本，`HEAD`表示当前版本，`HEAD^`表示上一个版本，`HEAD^^`表示上上版本，往上100个版本可以写成`HEAD~100`。
`git reflog`记录每一次命令，可以查看版本ID号，通过ID号可以回退到指定的版本，ID号不需要写全。

## 撤销修改
```git
$ git checkout -- Unity.md
```
把文件在工作区的修改全部撤销，有两种情况：
一种是文件自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
一种是文件已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

```git
$ git reset HEAD Unity.md
```
可以把暂存区的修改撤销掉（unstage），重新放回工作区。

## 删除文件
```git
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
```
在文件管理器中把test.txt文件删除后，如果确定要删除文件，使用命令`git rm`（同`git add`），再`git commit`，可将文件从版本库中删除。

```git
$ git checkout -- test.txt
```
如果是误删文件，可以使用上面的命令把被删的文件恢复到最新版本。
`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

## GitHub远程仓库

1. 创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果有则跳过。
```git
ssh-keygen -t rsa -C "email@example.com"
```
2. 登陆GitHub，在“Settings”，“SSH keys”中粘贴`id_rsa.pub`文件的内容。
3. 在GitHub上创建仓库，并在本地仓库下运行以下命令
```git
$ git remote add origin https://github.com/haipengyuan/day-day-up.git
$ git push -u origin master
```
`git push`命令是把当前分支master推送到远程。由于远程库是空的，我们第一次推送master分支时，加上了`-u`参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

克隆Github上的仓库：

```git
$ git clone git@github.com:haipengyuan/day-day-up.git
```

## 其他操作
### 配置别名
```git
$ git config --global alias.st status
```
加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
