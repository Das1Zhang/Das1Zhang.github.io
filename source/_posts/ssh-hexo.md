---
title: 使用ssh部署hexo
date: 2025-05-25 03:18:53
keyword: ssh, hexo 
description: deploy hexo by ssh
categories: CS工具
tags:
  - hexo 
  - CS工具
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hexobanner.jpg
  banner_text: Deploy hexo by ssh
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/ssh.png]
author: Das1
---

>我们使用https进行部署时，经常会发生连接不稳定，也就是经典的网络问题，本文介绍了使用ssh密钥来部署hexo从而避免网络连接不稳定的问题

## 更改deploy部分
找到_config.yaml文件，然后找到deploy部分修改为类似以下的内容（以我的博客仓库`Das1Zhang.github.io`为例：
``` yaml
deploy:
  type: git
  repo: git@github.com:Das1Zhang/Das1Zhang.github.io.git
  branch: gh-pages
```
主要是更改repo部分

## 配置 SSH 密钥
### 生成 SSH 密钥
打开 git bash 输入下面的内容
```bash
ssh-keygen -t rsa -C "your_email@example.com"
```
上面对应地填你的git邮箱

然后会出现让你填写保存key的路径：
```bash 
Enter file in which to save the key (/c/Users/Vodka/.ssh/id_rsa):
```
我们直接按下回车，使用默认路径即可，方便git自动识别

然后会看到两步提示：设置密钥密码和确认密码
我们也是直接按回车，不设置密码，这样每次使用git都不需要输入密码了
完成后会看到下面的信息：
```bash
Your identification has been saved in /c/Users/Vodka/.ssh/id_rsa
Your public key has been saved in /c/Users/Vodka/.ssh/id_rsa.pub
```

### 查看公钥内容
接下来我们要做的就是查看公钥内容，输入下面这段：
```bash
cat ~/.ssh/id_rsa.pub
```
我们把整段以 `ssh-rsa` 开头的内容复制下来

## 添加到Github
在GitHub中按照以下步骤查找：**Settings → SSH and GPG keys → New SSH key**
Title可以随意填，Key则填写我们刚刚复制的公钥内容，然后点击 `Add SSH key` 即可

## 测试ssh连接是否成功
git bash 中输入以下内容：
```bash
ssh -T git@github.com
```
如果看到：
```bash
Hi Das1Zhang! You've successfully authenticated, but GitHub does not provide shell access.
```
就说明配置已经成功了

## 重新部署
最后我们再次运行
```bash
hexo clean
hexo g
hexo d
```
应该就可以看到部署成功了
