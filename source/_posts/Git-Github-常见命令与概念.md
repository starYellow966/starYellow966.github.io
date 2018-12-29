---
title: Git & Github 常见命令与概念
date: 2018-12-29 00:05:29
tags: git
categories: 必备技术
---
# 一、常见命令

- `git init` : 初始化 git 仓库，即将一个文件夹初始化为一个 git 仓库。具体的操作是创建一个 `.git` 隐藏文件夹
- `git status` : 查看仓库的状态
- `git add <file>` : 将文件提交到暂存区
- `git commit -m <代码提交信息>` : 将暂存区的文件提交到仓库中，并附带说明信息
- `git log` : 查看所有产生的 commit 记录
- `git config -l` : 查看自己的配置，默认配置都在`.git/config` 文件中

## 1.2 分支相关的命令
- `git branch <branch_name>` : 如果不加上 `<branch_name>`，查看当前分支情况。如果加上，就是创建一个分支。
- `git checkout <branch_name>` : 切换到`<branch_name>`的分支上
- `git checkout -b <branch_name>` : 创建一个分支并切换到这个分支上，效果相当于合并上面 2 个命令。
- `git merge <branch_name>` : 将 `<branch_name>` 分支合并到当前所在的分支上
- `git branch -d <branch_name>` : 将 `<branch_name>` 分支删除
- `git branch -D <branch_name>` : 将 `<branch_name>` 分支强制删除。如果 `<branch_name>` 分支存在未合并的代码时，那么用 `-d` 是删除不了。

## 1.3 标签相关的命令
- `git tag ` : 查看历史 tag 记录。
- `git tag <tag_name> <commit_id>` : 在指定提交 id 上创建一个 tag。如果不写 `<commit_id>` ，那就在最新的 commit 上创建一个 tag。
- `git checkout <tag_name>` : 切换到 `<tag_name>` 标签
- `git show <tag_name>` : 查看标签信息
- `git tad -d <tag_name>` : 删除标签
- `git push origin <tag_name>` : 将某个标签推送到远程仓库
- `git push origin :refs/tags/<tag_name>` : 可以删除一个远程标签。

## 1.4 SSH

提前申明， windows 系统并不自带 SSH， 安装了 Git 之后才带有 SSH。

- `ssh-keygen -t rsa` : 指定 rsa 算法生成密钥。
- `ssh -T git@github.com` : 测试 ssh 是否成功添加到 github 中

## 1.5 Github 操作

- `git clone ...` : 将 github 项目复制到本地的当前目录
- `git push origin <branch_name>` : 把当前分支中本地代码同步到远程 <branch_name> 分支
- `git pull orgin <branch_name>` : 把远程 <branch_name> 分支的最新的代码同步到本地当前分支中
- `get remote add origin git@github.com:xxx/xxxx.git` : 将当前本地仓库与远程进行联接
- `git remote -v` : 查看我们当前项目有哪些远程仓库

## 1.6 补充命令

- **alias** 别名
  - `git config --global alias.<别名> "<原命令>"`。比如 `git config --global alias.c "checkout"`，之后 `git c` == `git checkout`
- **diff**
  - `git diff ` : 比较工作区文件和暂存区文件差异。如果加上文件名，就仅比较这个文件在工作区与暂存区的区别
  - `git diff <commit_id1> <commit_id2>` : 比较两次提交之间的差异

- **checkout**
  - 作用 1 ：切换分支、标签以及 commit。本质是「用某个 HEAD 中的最新内容替换掉你的工作区中的文件」。切换时，暂存区的内容不受影响但相当于在切换前执行了 `git stash`。
  - 作用 2 ：撤销还没进入暂存区的修改的作用。举个例子，假设我们在一个分支开发一个小功能，刚写完一半，这时候需求变了，而且是大变化，之前写的代码完全用不了了，好在你刚写，甚至都没有 git add 进暂存区，这个时候很简单的一个操作就直接把原文件还原：`git checkout a.md`。注意，checkout 命令只能撤销还没有 add 进暂存区的文件。
- **stash**
  - 作用：把当前分支所有还没有 commit 的代码先暂存起来。
  - 运用场景：假设我们正在一个新的分支做新的功能，这个时候突然有一个紧急的bug需要修复，而且修复完之后需要立即发布。当然你说我先把刚写的一点代码进行提交不就行了么？这样理论上当然是ok的，但是这会产品垃圾commit，原则上我们每次的commit都要有实际的意义，你的代码只是刚写了一半，还没有什么实际的意义是不建议就这样commit的，那么就用 `git stash` 保留。
  - `git stash` : 把当前分支所有没有 commit 的代码先暂存起来
  - `git stash list` : 查看所有 stash 记录
  - `git stash apply` : 将暂存的代码还原
  - `git stash drop` : 把最近一条 stash 记录删除。*注意：每次还原代码后，最好删除这条 stash 记录*
  - `git stash pop` == `git stash apply` + `git stash drop`
  - 注意：其实，执行了  `git add` 的数据在切换分支时，会自动暂存一起。本质上，是因为切换分支不影响暂存区的数据。但需要手动恢复。

# 二、常用概念
## 2.1 工作区、暂存区与 HEAD

- 工作区就是你还没有执行 `git add` 的文件，它持有实际文件；
- 暂存区就是你执行了 `git add` 但没执行 `git commit` 的文件，它像个缓存区域，临时保存你的改动；
- HEAD就是你执行完 `git commit` 文件，它指向你最后一次提交的结果。

![工作区、暂存区与 HEAD   参考：http://rogerdudler.github.io/git-guide/index.zh.html](https://upload-images.jianshu.io/upload_images/11455432-1fe05ddcc8b12d0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在实际中，工作区有一个隐藏目录 `.git` ，它不属于工作区，而是 Git 的版本库。Git 的版本库里存了很多东西，其中最重要的就是称为 stage（或者叫 index ）的暂存区，还有Git为我们自动创建的第一个分支 master，以及指向master的一个指针叫 HEAD。

![实际目录中，三者的联系 参考：https://www.liaoxuefeng.com](https://upload-images.jianshu.io/upload_images/11455432-d6cf9c4d7bfa7c0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2.2 分支 branch

[stormzhang 从0开始学习 GitHub 系列之「团队合作利器 Branch」](https://zhuanlan.zhihu.com/p/21487817)

## 2.3 标签 tag

本质：标签作用于提交上，可以看成是某个提交的别称。

运用：主要用于标记软件的版本号。

## 2.4 SSH

简单点说，SSH是一种网络协议，用于计算机之间的加密登录。目前是每一台 Linux 电脑的标准配置。而大多数 Git 服务器都会选择使用 SSH 公钥来进行授权，所以想要在 GitHub 提交代码的第一步就是要先添加 SSH key 配置。

命令生成的密钥会存放在两个文件 **id_rsa** 和 **id_rsa.pub** ，而 **id_rsa** 是密钥，**id_rsa.pub** 就是公钥。这两文件默认分别在如下目录里生成：

Linux/Mac 系统 在 `~/.ssh` 下，win系统在 `/c/Documents and Settings/username/.ssh` 下，都是隐藏文件，相信你们有办法查看的。

接下来要做的是把 **id_rsa.pub** 的内容添加到 GitHub 上，这样你本地的 **id_rsa** 密钥跟 GitHub 上的 **id_rsa.pub** 公钥进行配对，授权成功才可以提交代码。

## 2.5 Github 操作

####  **Push** 

如果你本地代码有更新，那么就需要把本地代码推到远程仓库，这样本地仓库跟远程仓库就可以保持同步了。

#### **Pull** 

如果远程仓库有更新，那么需要把远程仓库的最新代码更新到本地，然后保证两端代码的同步。**一般我们在 push 之前都会先 pull ，这样不容易冲突。**

#### 先有远程仓库，后有本地仓库：**Clone** 

使用 `git clone` 命令，将远程仓库复制到本地中。然后，修改本地仓库并进行 `git commit`。之后，`git push origin master` 将本地仓库的修改提交到远程仓库中

通过 `git clone` 命令复制的仓库，不需要使用 `git init` 初始化，同时也不需要声明与远程仓库关联（因为已经声明了）。

#### 先有本地仓库，后有远程仓库：关联本地已有项目

如果我们本地已经有一个完整的 git 仓库，并且已经进行了很多次 commit ，我们想将本地仓库提交到 github 上。

假设本地仓库 test2，要提交到 github test 项目中，一般步骤是：

1. 在 github 创建 test 项目
2. 在 test2 目录中输入 `git remote add origin git@github.com:xxxx/test.git` 将本地 test2 项目与 github 的 test 项目关联起来
3. `git push origin master` 

#### 合并操作：merge & rebase

`git merge branch_a` 与 `git rebase branch_a` 的效果是一样，都是将 `branch_a` 分支合并到当前的分支中。但两者合并方式不同：rebase 跟 merge 的区别你们可以理解成有两个书架，你需要把两个书架的书整理到一起去，
- 第一种做法是 merge ，比较粗鲁暴力，就直接腾出一块地方把另一个书架的书全部放进去，虽然暴力，但是这种做法你可以知道哪些书是来自另一个书架的；
- 第二种做法就是 rebase ，他会把两个书架的书先进行比较，按照购书的时间来给他重新排序，然后重新放置好，这样做的好处就是合并之后的书架看起来很有逻辑，但是你很难清晰的知道哪些书来自哪个书架。

#### 合并冲突

如果两个分支 a,b 同时修改了同一个位置时，合并到 master 时就会报冲突。不管两个分支合并的先后顺序、中间是否还合并了其他的分支（即任何情况下），都会报冲突。

虽然会报冲突，但在发生冲突的位置，会显示出两个分支修改的内容。你需要手动地进行正确的合并后，再 `git add .` 与 `git commit` 就可以了。


# 参考：
1. [stormzhang 从 0 开始学习 GitHub 系列](https://zhuanlan.zhihu.com/p/21487817)
2. [git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)