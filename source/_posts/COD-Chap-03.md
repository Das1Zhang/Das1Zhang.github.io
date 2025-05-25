---
title: Chapter_03 of COD
keyword: arithmetic
description: The arithmetic in COD
categories: 计算机组成原理
tags:
  - COD
  - CS-Learning
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/CODbanner.gif
  banner_text: Instruction in different format
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/CODcover.jpg]
author: Das1
---
- Operation on integers
	- Addition and substraction
	- Multiplication and division
	- Dealing with Overflow
- Floating-point real numbers
	- Representing and operations

# Addition 加法
- 从低位到高位逐个相加（考虑进位）
- 溢出判断
	- 一正一负相加不会发生溢出
	- 两个正数相加
		- 符号位为 1 则发生溢出
	- 两个负数相加
		- 符号为为0 则发生溢出

# Substraction 减法
- 也就是加上第二个操作数的负数（补码运算）
- 溢出判断
	- 两正或两负相减都不会发生溢出
	- 正数-负数
		- 符号位为1发生溢出
	- 负数- 正数
		- 符号位为0发生溢出

# 乘法
![乘法硬件电路](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/%E4%B9%98%E6%B3%95%E7%A1%AC%E4%BB%B6%E7%94%B5%E8%B7%AF.png)
乘法按照以下步骤执行：
1. 测试乘数第0位
	1. 如果为1 则将被乘数加到product中
	2. 如果为0 则不操作
2. 将被乘数左移一位
3. 将乘数右移一位
4. 是不是第32次重复
	1. 不是则循环
	2. 是则结束

### 改进的乘法器
上面的乘法器的硬件电路有些复杂，将上面的硬件改进简化一下如下图
![改进的乘法器](https://132-1331126615.cos.ap-guangzhou.myqcloud.com/%E6%94%B9%E8%BF%9B%E7%9A%84%E4%B9%98%E6%B3%95%E5%99%A8.png)
将原来的乘数寄存器放在了product的右半部分
每次进行乘法时
- 原来是每次将被乘数左移
- 现在是将整个product寄存器进行右移
	- 因为每次乘法时product都会多一位，而被乘数会少一位