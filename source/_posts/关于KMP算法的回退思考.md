---
title: 关于KMP算法的回退思考
keyword: KMP
description: Thinking about KMP algorithm
categories: 数据结构
tags:
  - hexo 
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hexobanner.jpg
  banner_text: The fundamental things about makefile
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/DS.jpg]
author: Das1
---
>初学KMP算法时，理解next数组以及回退过程是一个超级劝退过程。如果实在理解不了的，可以直接背。虽然作为十大经典算法之一，但是并不是非常重要，也就考试会考到罢了。
## 关键数据结构解释
- next数组：`next[k]`是`t[0]~t[j-1]`这个串的最大相同前缀的后一个地址，同时也表示最大相同前缀的数量。
- s串，t串：表示两个索引 j,k 在进行匹配时所指代的字串
## next数组是什么？
求next数组实际上就是求对于该串的每个字符而言，该字符前面的串 的 **最大前后相同前缀**  的 **前缀的后一个字符**。
这样以便我们可以快速在模式串与母串失配时，直接根据该失配字符的索引 j 找到其前面字串的最大相同前后缀的 前缀的后一个字符 `next[j]` 从而直接进行回退再次匹配。

## 求解next数组
求解next数组如下：
```cpp
void GetNext(string t, int* next)
{
	int j = 0, k =  -1;
	next[0] = -1;
	while(j < t.length() - 1)
	{
		if(k == -1 || t[j] == t[k])
		{
			j++, k++;
			next[j] = k;
		}
		else k = next[k];
	}
}
```
在求解next数组时，需要将k,j，可以看作两字串 s, t 同时做比较，在匹配过程中会出现几种情况：
- 匹配成功：k和 j 同时后移，记录`next[j] = k`
- 匹配失败：`k = next[k]`然后再次尝试匹配
- `k == -1`：说明已经回退结束，无法再回退了，k和j同时后移，记录`next[j] = k`，也就是该 j 位置的`next[j] = 0`，实际上可以理解为`k++;j++;next[j] = 0`，只不过这里把两步合并了

### 这里的`k = next[k]`是怎么来的？
首先`next[k]`表示 t 串前面字串的**最大相同前缀**的后一个字符地址，而同时，这个**最大相同前缀**也同时是 s 串的**最大相同前/后缀**，关键在于后缀。这就说明t串的前缀和s串的后缀已经匹配成功了，这时再次尝试匹配`next[k]`和`j`，如果匹配成功，则执行上述匹配成功操作，否则继续执行`k = next[k]`

## 形象的比喻
可以把这个过程想象为：
- 你在尝试用多个不同长度的尺子来测量模式串的前缀匹配
- 最长的尺子不合适时，你换上次长的尺子（通过`next[k]`获得）
- 这样逐步尝试更短的匹配可能性，而不是每次都从头开始

## 完整的KMP算法
求解next数组见前文即可。
```cpp
int KMP(string s, stirng t)
{
	int n = s.length(), m = t.length();
	int* next = new int[m];
	GetNext(t, next);          // 求next数组
	int i = 0, j = 0;
	while(i<n && j<m)
	{
		if(j == -1 || s[i] == t[j])    //j = -1 或 比较字符相同
		{
			i++; j++;          //i，j递增
		}
		else j = next[j];      //匹配不成功，i不变，j回退
	}
	if(j >= m) return i - m;   //t串遍历完毕，匹配成功
	else return -1;            //t串未遍历完，匹配失败，返回-1
}
```
### KMP算法的用处
与暴力算法相比，KMP算法不需要对主串进行回溯，而是直接通过记录模式串的信息，只需要对模式串进行回溯即可。
## KMP算法的改进
### 改进next数组的求解
```cpp
void GetNextval(string t, int* next)
{
	int j = 0, k =  -1;
	next[0] = -1;
	while(j < t.length() - 1)
	{
		if(k == -1 || t[j] == t[k])
		{
			j++, k++;
			if(t[j]!=t[k])           // 如果两个字符不相等，直接记录
				nextval[j] = k;
			else
				nextval[j] = nextval[k]; // 如果两个字符相等，那么再进行一次回退
		}
		else k = nextval[k];
	}
}
```
改进的算法的意思就是，我们原来记录`next[j] = k`是表示如果 j 位置失配了，那么下一次就回退比较 k 的位置。
但是如果回退后和之前我们失配时的字符是一样的，那回退不就是没有意义的吗？所以我们在这里多做一次比较：
- 如果回退之后的字符不同`t[j]!=t[k]`，那么下一次可以在这里进行匹配，正常记录回退位置。
- 如果回退之后字符仍然相同`t[j]==t[k]`，那我们就直接不需要再考虑了，直接进行再次回退，因为我们根据前一次知道，这次一定会失配，那么就记录再次回退后的位置。
### 改进的KMP算法
```cpp
int KMPval(string s, stirng t)
{
	int n = s.length(), m = t.length();
	int* nextval = new int[m];
	GetNextval(t, next);          // 求next数组
	int i = 0, j = 0;
	while(i<n && j<m)
	{
		if(j == -1 || s[i] == t[j])    //j = -1 或 比较字符相同
		{
			i++; j++;          //i，j递增
		}
		else j = nextval[j];      //匹配不成功，i不变，j回退
	}
	if(j >= m) return i - m;   //t串遍历完毕，匹配成功
	else return -1;            //t串未遍历完，匹配失败，返回-1
}
```
改进后的KMP算法的逻辑和改进前一模一样。也就是说，只是对求解next数组的过程进行了改进。