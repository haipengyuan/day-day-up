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

## 基本操作

* 初始化仓库

```git
$ git init
Initialized empty Git repository in G:/Code/git/.git/
```

初始化成功之后会在当前目录生成 `.git` 目录。

* 查看仓库状态

```
$ git status 
```

* 向暂存区中添加文件

```
$ git add Unity.md
```

暂存区是提交之前的一个临时区域。

* 保存仓库的历史记录

```
$ git commit -m "First commit"
```

将当前暂存区中的文件保存到仓库的历史记录中。通过这些记录，就可以在工作树中复原文件。

`-m "First commit"` 为提交信息，是对这个提交的概述。

```
$ git commit
```

`git commit` 不加 `-m` 参数，会启动编辑器，可以记录提交的详细信息，格式如下：

* 第一行：用一行文字简述提交的更改内容
* 第二行：空行
* 第三行以后：记述更改的原因和详细内容

只要按照上面的格式输入，今后便可以通过确认日志的命令或工具看到这些记录。

若提交信息为空并关闭编辑器，则提交会被中止。

* 修改提交信息

```
git commit --amend
```

可以修改上一条提交信息。

* 查看提交日志

```
$ git log
```

可以查看什么人在什么时候进行了提交或合并，以及操作前后有怎样的差别。

* 只显示提交信息的第一行

```
$ git log --pretty=short
```

* 只显示指定目录、文件的日志

```
$ git log README.md
```

`git log` 后加上目录名，便会只显示该目录下的日志。加上文件名，就会只显示与该文件相关的日志。

* 显示文件的改动

```
$ git log -p
$ git log -p README.md
```

* 查看工作树和暂存区的差别

```
$ git diff
```

`+` 号标出的是新添加的行，`-` 号标出的是被删除的行。

* 查看工作树和最新提交的差别

```
$ git diff HEAD
```

`HEAD` 是指向当前分支中最新一次提交的指针。

## 分支操作

不同分支中，可以同时进行完全不同的作业，每个分支中都拥有自己的最新代码。`master` 分支是 git 默认创建的分支。

* 显示分支一览表

```
$ git branch
* master
```

`master` 分支左侧标有 `*` ，表示这是当前所在分支。

* 创建、切换分支

```
$ git checkout -b feature-A
```

以上命令等同于

```
$ git branch feature-A
$ git checkout feature-A
```

* 切换回上一个分支

```
git checkout -
```

* 特性分支

特性分支是集中实现单一特性（主题），除此之外不进行任何作业的分支。在日常开发中，往往会创建数个特性分支，同时在此之外再保留一个随时可以发布软件的稳定分支。稳定分支的角色通常由 `master` 分支担当。

![image-20210609174540307](D:\Code\git\day-day-up\Git.assets\image-20210609174540307.png)

* 主干分支

主干分支是特性分支的原点，也是合并的终点。通常用 `master` 分支作为主干分支。主干分支中并没有开发到一半的代码，可以随时供他人查看。

* 合并分支

将 `feature-A` 分支合并到 `master` 。

```
$ git checkout master
$ git merge --no-ff feature-A
```

随后编辑器会启动，用于录入合并提交的信息。

* 以图表形式查看分支

```
$ git log --graph
```

## 更改提交的操作

* 回溯历史版本

```
$ git reset --hard 目标时间点哈希值
```

* 推进历史

```
$ git reflog
```

可以查看当前仓库执行过的操作的日志。

* 压缩历史

```
$ git rebase -i HEAD~2
```

可以选定当前分支中包含HEAD（最新提交）在内的两个最新历史记录为对象，并在编辑器中打开，将两次提交的内容合并成一个新的提交。

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
`git remote add` 命令之后，Git 会自动将远程仓库的名称设置为 origin 。

`git push`命令是把当前分支master推送到远程。由于远程库是空的，我们第一次推送master分支时，加上了`-u`参数，Git 不但会把本地的master分支内容推送到远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。


* 推送至master以外的分支

```
$ git checkout -b feature-D
$ git push -u origin feature-D
```
* 克隆Github上的仓库

```git
$ git clone git@github.com:haipengyuan/day-day-up.git
```

执行 `git clone` 命令后会默认处于 `master` 分支下，同时系统会自动将 `origin` 设置成该远程仓库的标识符。

```
$ git branch -a
```

添加 `-a` 可以同时显示本地仓库和远程仓库的分支信息。

* 获取远程的其他分支

```
$ git checkout -b feature-D origin/feature-D
```

`-b` 后面是本地仓库中新建分支的名称，新建分支名称后面是获取来源的分支名称。即以名为 `origin` 的仓库的 `feature-D` 分支为来源，在本地仓库中创建 `feature-D` 分支。

* 获取最新的远程仓库分支

```
$ git pull origin feature-D
```

## 其他操作

### 配置别名
```git
$ git config --global alias.st status
```
加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
