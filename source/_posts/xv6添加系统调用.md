---
title: 为risc-v xv6添加系统调用
keyword: xv6
description: add a system call to xv6
categories: OS
tags:
  - OS
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hexobanner.jpg
  banner_text: Add a System Call to xv6.
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hmmmm.jpg]
author: Das1
---

>本教程针对riscv版本的xv6，其部分文件有出入，某些网上的文章并不适用该xv6系统（比如usys.S文件在该版本下为usys.pl经过make之后产生的汇编语言文件，不可以直接进行修改），具体参考：[riscv xv6源码](https://github.com/mit-pdos/xv6-riscv)

## 基本流程
1. 【添加内核接口】在kernel目录下修改 syscall.c和syscall.h（以添加psstate系统调用为例）
	1. kernel/syscall.c 中添加 `[SYS_psstate]   sys_psstate`
	2. kernel/syscall.h 中添加`#define SYS_psstate 22`
2. 【实现】在sysproc.c中增加系统调用的实现
3. 【增加用户入口】在usys.pl文件中添加系统调用入口`entry("psstate");`
4. 【修改Makefile】在makefile中添加命令`$U/_hello\
5. 【添加源文件】在user.c命令下添加源文件 psstate.c
6. 【函数声明】在user.h中声明函数 `psstate(int state_flag)`

## 原理解析（最自然的添加过程）
>对于OS初学者来说（本人T~T），直接上手接触xv6源码添加系统调用会对各种文件感到一头雾水，只会傻乎乎地跟着教程做，这里会解释添加一个系统调用的原理以及各个文件之间的联系。
>这里我会从“用户->内核->用户”的系统调用过程来解释一下这个系统调用是如何实现的。

### 声明函数调用
找到user/user.h，声明我们需要添加的函数，对于用户态来说，系统调用其实就是函数，没啥两样
添加下面这一行
```cpp
int psstate(int state_flag);
```
### 添加用户态的入口
我们声明需要的函数之后，接下来是不是要跳转到内核态来执行系统调用了呢？
所以我们找到user/usys.pl添加下面这一行
```cpp
enter("psstate");
```
这样我们的入口就添加好了

### 如何“用户->内核”？？
按照我们教科书上的说法是，**“用户->内核”是一个“陷入陷阱”的过程**
我们仔细阅读一下usys.pl就会发现，在entry上面有这样一段代码：
```cpp
sub entry {
    my $name = shift;
    print ".global $name\n";
    print "${name}:\n";
    print " li a7, SYS_${name}\n";
    print " ecall\n";
    print " ret\n";
}
```
这段代码实际上就是一个用于产生汇编程序的代码（很明显的汇编语言），在我们进行make之后，可以发现新产生了一个usys.S文件，里面的内容就是由上面代码产生的汇编语言程序。
![usys汇编](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/usys%E6%B1%87%E7%BC%96.png)
那么这段汇编又是什么意思呢？
`li a7, SYS_psstate` 就是说将 **SYS_psstate放入 a7 寄存器**中
欸，这个ecall又是啥呀？这给人看蒙了，也没有说跳转到哪个函数中啊？
而这就是我们从用户态到内核态的关键
我们可以在kernel/trap.c中的`usertrap(void)`中看到这么一段
![usertrap](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/uesrtrap.png)
看到这个`r_scause() == 8`和下面我们心心念念的system call了吗，说明如果r_scause() == 8 就捕获到了触发系统调用的入口，这就是我们的ecall的来源

### 触发系统调用
调用syscall()之后，我们转到kernel/syscall.c中
![syscall](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/syscall.png)
发现这里会创建一个进程 p，而num则会从该进程的 trapframe中取 a7寄存器的值，然后该系统调用的返回值会放回到该进程 trapframe 的 a0寄存器
根据下面的注释我们就知道，通过**这个num值来查表**，看哪个系统调用应该被调用
这个系统调用的表也就是 syscalls，同样的也在kernel/syscall.c中

那么我们现在就可以在这个表中添加我们需要的系统调用，让`syscalls[num]`可以正确索引，如下所示：
![syscalls表](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/syscalls%E8%A1%A8.png)
左边的索引就是我们从a7中取出来的num，映射到对应的系统调用。

这里另外提一嘴c语言特性，**这个`syscalls[num]`实际上是一个函数指针**，这样就可以解释为什么我们可以直接跳到sysproc.c中进行函数的实现了。
实际上这个说法也不严谨，我们的**系统调用实际上已经在sysproc.c中实现好了**，只是通过`syscall[num]()`进行调用而已
### 实现系统调用
现在既然我们的系统调用已经可以被正确的找到并调用了，那么让我们来实现一下该系统调用，这里就是正常的函数实现逻辑
![实现系统调用](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/%E5%AE%9E%E7%8E%B0%E7%B3%BB%E7%BB%9F%E8%B0%83%E7%94%A8.png)
注意这里这里是内核态的实现，我们查查`argint()`函数，其定义如下：
```c
void
argint(int n, int *ip)
{
  *ip = argraw(n);
}
```
这个函数取an寄存器的值，放到ip指针里，而an是参数寄存器
该系统调用取a0寄存器中的值作为state_flag，然后根据state_flag 做出对应的操作。

#### 系统调用和用户态的声明
这里又有一个疑点了，欸我们在用户态的系统调用声明不是说有一个参数就是state_flag吗？**实际上我们进行函数调用时，各个参数就会放到对应的寄存器中准备进行调用**，我们在sysproc.c中的实现的系统调用就要从各寄存器中把需要的参数拿过来
这个过程可以这样理解：
1. 用户态触发系统调用，各个参数被放到对应的寄存器中，准备进行系统调用
2. 陷入内核态
3. 系统调用从对应的寄存器中把参数拿过来，然后使用

系统调用结束后我们的返回值又放在a0了，这样`syscall()`就结束了，然后调用`usertrapret()`返回用户态

## 用户程序
我们最后不是还编写了一个用户程序 psstate.c 吗？
实际上我们在命令行输入psstate之后，调用的就是这个程序，所有的运行逻辑都是按照这个程序实现的。

这时候又有疑问了：欸我们的系统调用不是在前面实现过了吗，为什么不是按照那个来运行的呢？

**实际上这个psstate.c 可以看作一个测试程序**，我们在命令行输入psstate之后，xv6实际上为我们**exec了一个按照psstate.c执行的程序**。

而如果我们把名字变为foo.c，那么在命令行中输入psstate就无法实现正确的调用了，这时候我们又要在命令行中输入 foo 来正确调用了。

## 关于makefile
而makefile，实际上**和我们的系统调用没啥关系**，他管理的是哪些文件编译进用户空间，哪些文件编译进内核，如何链接。
我们在makefile中添加`$U/_psstate\`是让psstate.c可以正确地编译，并生成可执行的 psstate 文件

## 总结
实际上我们添加系统调用的过程，很好地遵循了操作系统对程序的操作规则，**“用户(.c用户程序) -> 内核(系统调用) -> 用户(继续执行.c程序)”**
执行系统调用的基本流程如下：
1. .c用户程序执行系统调用函数（提前在 user/user.h 中声明）
2. user/usys.pl 生成 user/usys.S 发出ecall，发出要陷入内核的信号（usys.pl中声明入口）
3. kernel/trap.c 中 `usertrap()` 捕捉 ecall信号，执行`syscall()`
4. kernel/syscall.c 中 `syscall()` 从当前进程帧的a7寄存器中取出值num，并在`syscalls[num]` 进行映射调用
5. kernel/sysproc.c 中进行函数的调用实现，返回到kernel/syscall.c将返回值存在当前进程帧的a0寄存器中
6. `syscall()`调用完毕，返回到kernel/trap的`usertrap()`，然后调用`usertrapret()`返回用户态
7. 至此系统调用完毕，继续执行.c用户程序