---
title: About Makefile
keyword: makefile,gnu,cmake
description: The fundamental things about makefile
categories: CS工具
tags:
  - makefile
  - CS-Learning
  - CS工具
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/gnucover.jpg
  banner_text: The fundamental things about makefile
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/gnucover1.jpg]
author: Das1
---

## 参考视频
[二十分钟makefile光速入门教程](https://www.bilibili.com/video/BV1tyWWeeEpp?vd_source=1365bc20e6f6575f5f1cfb2ee106eb3e)
<iframe src="//player.bilibili.com/player.html?bvid=BV1tyWWeeEpp&autoplay=0" 
        scrolling="no" 
        border="0" 
        frameborder="no" 
        framespacing="0" 
        allowfullscreen="true" 
        width="720" 
        height="480">
</iframe>


## 安装 gnu 和 cmake
Linux中直接使用 `sudo apt install ` 就可以直接安装

## 一个简单的makefile文件配置
```bash
.PHONY: clean all
CFLAGS = -Wall -g -O2
targets = hello world
sources = main.c message.c
objects = main.o message.o

all: $(targets)
	echo "all done"
$(targets): $(objects)
	gcc $(CFLAGS) $(objects) -o $@

main.o: main.c
	gcc $(CFLAGS) -c main.c

message.o: message.c
	gcc $(CFLAGS) -c message.c

clean:
	rm -f *.o hello world
```

## 基本规则
```bash
targets : dependencies
	actions
```
makefile 的基本规则简单来说就是
- 为了什么 ——targets
- 依赖什么 ——dependencies
- 怎么做  ——actions

例如
```bash
main.o: main.c
	gcc -c main.c
```
这段代码的目标文件就是main.o，其依赖于 mian.c，完成这段行为的命令就是 `gcc -c main.c`

## 自定义变量
通过`targets = main` 来自定义一个变量
在后续的编写中可以用 $(targets) 来代替 main


## 自动变量
`$@`自动替换为该语句的 targets
例如可以这样写：
```bash
main: main.o message.o
	gcc main.o message.o -o $@
```

`$^` 自动替换为第一个依赖文件
例如可以这样写：
```bash
message.o: message.c
	gcc -c $^ -o $@
```

### 通配符
在生成 mian.o 和 message.o 文件时可以看到，二者的生成规则是相同的，只是文件名不一样，可以使用通配符来替换文件名，例如可以这样写
```bash
%.o: %.c
	gcc -c $^ -o $@
```
这样就表示所有的.o文件都是由.c 文件通过下面的操作完成的

## make 参数
`make`指令后面是可以添加参数的

默认情况下，make会自动识别makefile，但是如果我们的make规则如果不是写在makefile文件中而是另一个文件（例如 hello.mk），则可以使用 -f 参数来指定文件
例如
```bash
make -f hello.mk
```
这样hello.mk 就充当了makefile的作用

-n: 来打印make执行的命令但是并不会真的执行
```bash
make -f hello.mk -n
```
 -C: 来指定make执行的目录
 例如当一个工程下有多个子模块，每个子模块有各自的makefile


## CMake
CMake 是一个**跨平台的构建工具**，可以根据不同的平台和编译器生成对应的构建文件，比如* nix 平台上可以生成makefile，Win平台上可以生成 Visual Studio 的项目文件。

其配置文件为： CMakeLists.txt

对于CMake可以简单地理解为 **由CMake配置文件生成makefile配置文件**，是一个更加方便快捷的构建工具

下面给出一个示例 CMakeLists.txt 配置文件内容
```CMakeLists.txt
cmake_minimum_required(VERSION 3.10)

project(HelloWorld)

set(SOURCES main.c message.c)

add_executable(hello ${SOURCES})
```
从上到下的四行语句分别作用为
- 指定cmake的最低版本要求
- 指定工程名字（可以随便取）
- 指定源文件列表（依赖文件）SOURCES 为一个变量
- 生成可执行文件

当我们执行 `cmake`命令后可以看到自动生成了Makefile，这就是上面所说的“由配置文件生成配置文件”，然后可以执行`make`命令，此时这个`make`命令所依赖的就是这个由CMake生成的Makefile
