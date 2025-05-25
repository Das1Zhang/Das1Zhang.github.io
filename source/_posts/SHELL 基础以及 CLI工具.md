---
title: SHELL基础以及CLI工具
keyword: SHELL, CLI
description: Introductions about SHELL and CLI tools
categories: CS工具
tags:
  - CS工具
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hexobanner.jpg
  banner_text: Introductions about SHELL and CLI Tools.
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/shellcover.jpg]
author: Das1
---
## 关于shell

### Prompt

> 命令提示符
> 

等待输入并告知 **工作路径 & 当前操作用户、**

~代表“home”目录

*nix 下的路径分隔符为 “/”，而 Win 则是 “\”

*nix 没有分盘概念，所有文件全部挂载在唯一根目录 / 下

## shell 基本命令

### 路径命令

`pwd`获取当前工作路径

```bash
pwd
```

切换路径

path可以是绝对路径也可以是相对路径

. 表示当前路径， .. 表示上一级路径

```bash
cd path

从/home/das/ 切换到 /user/bin/
cd ../../user/bin 或 cd /user/bin

从/user/ 切换到 /user/games/
cd games 或 cd ./games 
```

### 文件 / 目录 操作命令

`ls` : 列出当前路径下的文件和目录

```bash
ls -a  # 包括隐藏文件
ls -l  # 列出详细信息
ls -al # 两条命令复用
```

`touch` : 创建一个文件

```bash
touch das.c
touch das.txt
```

`mkdir` : 创建一个目录

```bash
mkdir das1
```

`cp`复制文件或目录

```bash
cp src dst
cp -r src dst  # 递归复制
```

`mv`移动文件或目录（重命名）

```bash
mv a b # 将 a 重命名为 b 
mv das.txt das.cpp # 将das.txt 重命名为 das.cpp
```

`rm`删除文件 （不会进入回收站）

```bash
rm -r # 递归删除目录
rm -f # 强制删除
```

`find`查找文件

```bash
find . -name das.c # 找到并打印出当前目录下的所有das.c文件
find . -name '*.c' # 找到并打印出当前目录下所有以.c结尾的文件
```

### 文件内容查看命令

`cat`：输出与拼接文件

```bash
cat a.c a.cpp # 一起输出两个文件
cat -n a.c a.cpp # 带行号输出
```

`head` `tail`输出前10行/后10行内容

```bash
head -n lines a.cpp # 输出a.cpp前lines行的内容
head -n 3 a.cpp # 输出a.cpp 前3行的内容
```

`more` `less` 分页输出文件内容

```bash
more a.cpp
less a.cpp
```

`hexdump` 十六进制输出文件内容

```bash
hexdump -C # 并排输出十六进制与ASCII
hexdump -n # 输出前 n 个字节
```

## 环境变量

> 命令是什么？→ 绝大部分就是可执行文件（bin/ls）
> 
> 
> 为什么shell通过 `ls` 就可以找到 /bin/ls/？ → 通过环境变量
> 
> 为什么一定要用`./a.out` 而不是`a.out` 来执行程序？ → . 并不在PATH变量中
> 

`echo $var` 来输出环境变量的值，`env` 查看所有环境变量

- 设置环境变量
    - `export var=value` ：设置环境变量
        - 仅在当前执行的shell中有效。持久化？写入配置文件
    - `unset var` ：删除环境变量

## 关于vim

### 几种模式

- Normal：默认模式，移动光标、处理文本、执行命令等
- Insert ：输入文本
- Command：执行命令
- * Visual：选择文本

![vimmode](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/vimmode.png)

## 关于 GNU Make

![gnutools](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/gnutools.png)