---
title: Hexo搭建 & 关键命令
keyword: The key commands about hexo
description: Key commands about Hexo
categories: CS工具
tags:
  - hexo 
  - CS工具
banner:
  type: img
  bgurl: https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hexobanner.jpg
  banner_text: How to Build Your Personal Blog.
toc: true
cover: [https://132-1331126615.cos.ap-guangzhou.myqcloud.com/hexocover.jpg]
author: Das1
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Hexo搭建
[Async主题配置文档](https://hexo-theme-async.imalun.com/guide/)
关于Hexo框架的搭建其实非常简单，使用GitHub page来进行托管
### 所需软件
- Node.js
- Git
- Hexo
安装Node.js后可以使用下面的指令来安装Hexo
```bash
npm install -g hexo-cli
```
你可以用以下指令来看看自己是否安装成功了
```bash
node -v
npm -v

git --version

hexo -v
```

### 关于图片
如果想把图片远程部署到GitHub page的话，不经过图片托管软件的话经过笔者的实验是非常麻烦并且很容易失败，最简单的方法就是将所有的图片全部放在图床上，我使用的是[腾讯云](https://www.tencentcloud.com/)+[PicGo](https://picgo.github.io/PicGo-Doc/zh/guide/#picgo-is-here),在文章中放入图床中的图片链接即可

### 本地初步搭建
初始化Hexo项目（my-blog是项目文件夹名称）
```bash
hexo init my-blog
cd my-blog
```

安装依赖
```bash
npm install
```

启动本地服务器
```bash
hexo server # 或 hexo s
```
在终端中可以看到一个本地网址：http://localhost:4000，访问即可预览网站状态

创建新文章
```bash
hexo new "newpage"
```
此时在 source/ _ posts 目录下就会生成一个文件名为 newpage.md的md文件

### 部署在GitHub pages
1. 首先在Github Pages创建一个新仓库，命名为`<用户名>.github.io`，例如我的仓库名称为 `Das1Zhang.github.io`
2. 获取仓库的https地址，例如我的就是`https://github.com/Das1Zhang/Das1Zhang.github.io`
3. 找到`_config.yml`文件中的 deploy 部分，将上面的地址填入repo，type设置为`git`，branch则设置为 `main`（这里填你的Github默认分支名）
4. 使用`hexo g`或`hexo generate`来生成静态文件
	1. 这里会生成静态文件到 public 目录
5. 然后使用`hexo deploy`进行部署，将public目录中的内容推送到 Github仓库的指定分支

### 主题选择
如果你按照以上步骤配置的话，在不出错的情况下你可以看到一个hexo的默认主题，当然如果你也可以在[主题市场](https://hexo.io/themes/)挑选一个你喜欢的主题，一般会配有对应的配置文档，按照你个人的风格进行自定义配置即可。

### 笔者主题
我使用的hexo主题为：[Async](https://github.com/MaLuns/hexo-theme-async)，[配置手册](https://hexo-theme-async.imalun.com/guide/)


## 常用指令 & 基本工作流
```bash
hexo new page   # 创建新文章

hexo clean      # 清除静态文件

hexo g          # 生成静态文件 

hexo s          # 启动本地服务器

hexo d          # 部署到托管平台

```

## 另外一提
如果你在运行hexo g或者任何hexo命令时，出现了下面的报错
```bash
hexo : 无法加载文件 C:\Users\Vodka\AppData\Roaming\npm\hexo.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink
/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1
+ hexo g
+ ~~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```
那么你可以打开powershell试试输入下面的指令
```bash
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```
问题在于PowerShell的执行策略阻止了hexo.ps1脚本的运行，Windows处于安全考虑，不允许运行某些脚本。
通过这个指令可以放宽PowerShell的执行策略。

## 关于插入图片和视频
### 插入图片
需要插入图片时可以使用下面的插入方式
```
![图片描述](图片地址)
```
### 插入视频
#### 插入本地视频(图床URL)
需要插入视频时，图床视频需要使用HTML5`<vedio>`标签进行插入
```
<video width="640" controls poster="https://example.com/preview.jpg">
  <source src="https://example.com/video.mp4" type="video/mp4">
</video>
```
例如我的视频地址为`https://132-1331126615.cos.ap-guangzhou.myqcloud.com/250522_fun2.mp4`，我就可以使用下面的代码进行插入：
```

<video width="720" height="400" controls poster="https://132-1331126615.cos.ap-guangzhou.myqcloud.com/preview.jpg">
  <source src="https://132-1331126615.cos.ap-guangzhou.myqcloud.com/250522_fun2.mp4" type="video/mp4">
</video>
```
#### 插入外部视频(远程网站)
而需要插入外部视频时，比如bilibili的视频，我们则需要使用
`iframe`嵌入方式，因为视频网站不提供.mp4直链，我们可以使用下面的代码：
```
<iframe src="//player.bilibili.com/player.html?bvid=BV1tyWWeeEpp&autoplay=0" 
        scrolling="no" 
        border="0" 
        frameborder="no" 
        framespacing="0" 
        allowfullscreen="true" 
        width="720" 
        height="480">
</iframe>
```
这样我们就可以直接插入BiliBili视频啦`^_^`

没错！md语法是可以和`<iframe>`或者HTML5进行混写的噢！
我们在Obsidian或者vscode的预览中都可以直接预览到我们插入后的效果。