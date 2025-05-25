---
title: VScode配置c&c++运行环境(win)
keyword: vscode
description: Configuration of vscode for c/cpp
categories: CS工具
tags:
  - CS工具
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/studybanner.gif
  banner_text: Configuration of vscode for c/cpp
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/vscodecpp.jpg]
author: Das1
---

## 参考视频
[vscode配置c/c++环境](https://www.bilibili.com/video/BV1tyAtetEd1/?spm_id_from=333.337.search-card.all.click&vd_source=1365bc20e6f6575f5f1cfb2ee106eb3e)

## 前置条件
- 安装拓展：C/C++
- [安装MinGW](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-posix/seh/)（安装解压后需配置环境变量）
 
## 关键步骤
### 配置C/C++插件
创建文件夹编写好一个c/cpp文件，此时按下`ctrl+shift+p`调出命令输入窗口，搜索C/C++ Edit Configuration(UI)。

点开后，找到编译器路径，将其修改为我们安装的MinGW的bin文件夹路径中的需要的编译器，例如`D:/MinGW64/mingw64/bin/g++.exe`(这里g++适用cpp环境，如果是c环境，则改为gcc.exe否则会导致链接错误)。

再往下翻找到IntelliSense模式，改为`windows-gcc-x64`

到这里C/C++插件就配置完成了
### 任务配置
上面一栏“转到”后面的省略号，然后终端->配置任务，可以看到我们的项目文件夹中自动生成了一个`.vscode`并带有`.json`文件的文件夹。

然后同样的，终端->生成运行任务(或者ctrl+shift+b)，这时会看到我们需要选择的编译器，c->gcc.exe，cpp->g++.exe

如果编译完成，就会看到项目文件夹中多出了一个.exe文件，这就是我们的可执行文件。在终端中输入`./test.exe`或`.\test.exe`都可以运行这个文件。至此我们的项目就正常运行了。