---
title: git
keyword: github, git, version control
description: The most popular version control system 
date: 2024-11-13 21:37:59
tags:
    - CS 工具
    - git
categories: CS 工具
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/CODbanner.gif
  banner_text: The most popular version control system 
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/gitcover.jpg]
author: Das1
---
## 本地git 操作流
`git init`:
在该文件夹本地创建一个 .git 仓库，相当于初始化，这将一个普普通通的文件夹进化成一个repo，可以进行各种git操作啦！


`git add`：
`git add` 用于将本地发生修改的文件（包括删除或者添加的文件）放入staging area，用于后续的commit


`git commit`:
一般命令为 `git commit -m "MESSAGE"`，用于提交当前的commit，本质上是将上一个commit 复制过来并将staging area中的内容合并进去。同时`git commit`操作会移动本地的HEAD poingter 和 master pointer


`git status`:
显示目前的git状态，可以显示你有哪些文件修改之后是否放入staging area。
记住 git 可以自动识别你对文件的操作，这就是`git status`的奥义所在


`git log`:
commit 日志，显示所有的commit记录，包括作者时间和commit时所写的message

## 进行版本控制
`git checkout`：
还记得我们之前提到的 HEAD pointer 和 master 吗？没错这就是版本控制操作的关键所在，HEAD pointer 会指向我们目前所在的commit，而master 则会指向最近提交的commit。

每个commit会有独属于自己的一个“ID”，运行`git checkout ID`就可以实现版本控制操作，“两极反转！”，这样就可以跳转这个ID所属的commit中，HEAD pointer也会随之改变了。

当然，如果我们想回来，master pointer 的作用就体现出来了，`git checkout master`就可以跳转到最近的commit啦！

## 使用 git 进行协作
首先如果需要进行协作，肯定是要使用一个云端的，比如GitHub

`git clone`:
使用`git clone url`指令可以将云端GitHub仓库克隆到本地仓库中，同时也会有一个同步于云端的origin/head pointer指向这个初始commit（是的，你可以将`git clone`操作视为一个commit，这没啥，对吧）


`git remote -v`:
显然这里 -v 是作为一个参数出现的，但是CS61B lab4中并没有对`git remote`指令本身进行介绍。
`git remote -v`指令可以展示当前的origin 信息，包括你从哪里fetch，将要push到哪里去


`git push`:
一般使用`git push origin master`将我们的master 分支push到远程的origin中，完成一个版本的更新（相当于我们在本地的commit，本地commit完成了本地文件的修改，而push相当于远程的commit）
而如果我们再进行协作时，远程仓库发生了更新，也就是远程仓库有了新的commit，我们的push请求就会被拒绝，此时就请出我们的`git pull`来了


`git pull`
一般使用`git pull origin master`从我们最开始clone下来的origin 拉取到我们的master分支这里来，并自动进行合并操作（是的，git就是这么聪明，比如两个人在同一个.java 文件中写了两个不同的函数，这样就可以直接合并了），并自动给出一个新commit，如果无法合并（比如两个人的同一行代码写法不同），这时就需要你手动进行修改，此时你打开发生冲突的文件就可以看到哪里发生了冲突，就像这样：
![merge conflict](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/merge%20conflict.png)
修改完之后，在将此次修改commit上去，就OK了。



注意一点：所有与这个云端仓库相关的commit，你都可以想象它们以时间顺序排列，一个协作者的commit并不会覆盖另一个写作者的commit，最终所有的commit仍然呈现一个完整的链，不会有commit缺失的情况。



还有一点关于origin/master pointer
如果你进行了push操作，此时origin/master pointer也会发生变化，也就是说origin/master pointer是随着远程仓库的master 发生动态变化的。如果没有进行push则不会发生改变



这时考虑一个问题：
如果你仅仅将一个文件进行了删除，此时你想找回这个文件，想要通过远程仓库作为一个备份来恢复这个文件，此时你运行pull，会发现显示一切都up-to-date，明明我已经删除了一个文件，怎么会up-to-date呢？

这是因为远程仓库并不是实时监控你的本地仓库，而是当有push操作时才会发生变化，而当没有操作时，你的origin/master 和 远程仓库的master 指向同一个commit，他认为这会是最新的（类似于你闭门造车，把你的绝世SCI藏着掖着，别人就会认为尚且没有新版本横空出世）。

那我们想要恢复，就只能通过`git log`来使用`gie checkout`来进行版本控制了。

### 关于 git remote -v补充：
添加远程仓库：
`git remote add <name> <url>`

删除远程仓库：
`git remote rm <name>`

重命名远程仓库：
`git remote rename <old_name> <new_name>`

查看远程仓库详细信息：
`git remote show <name>`

更改远程仓库 url：
`git remote set-url <name> <url>`

多个远程仓库协作：
`git remote add <second_name> <second_url>`

从远程仓库拉取更新：
`git fetch <name>` 

## 在多个仓库之间进行协作
关键是搞清楚每个仓库当前的master pointer，当我们进行`pull`和`push`操作时的逻辑也是一样的。
