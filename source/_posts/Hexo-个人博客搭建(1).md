---
title: Hexo 个人博客搭建（1）
tags:
  - Hexo
  - Github
  - 教程
categories:
  - Hexo
date: 2021-11-18 17:22:43
---

# 前言

hexo的官网是https://hexo.io/zh-cn
官网中有详细的搭建方法，但是在自己的电脑实际使用中会有坑，所以记录一下自己的搭建过程。
本篇主要记录Hexo博客的安装和基础使用指令。</br>
Hexo搭建的系列：
[Hexo 个人博客搭建（1）](https://minamisama.github.io/2021/11/18/Hexo-%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA(1)/)
[Hexo 个人博客搭建（2）](https://minamisama.github.io/2021/11/19/Hexo-%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA(2)/)

<!--more-->

# 事前准备

## 安装nodejs
```
$ sudo apt-get install nodejs
```
</br>

## 安装npm
```
$ sudo apt-get install npm
```

npm安装完成后需要执行以下命令用于更新包
```
$ sudo npm i
```
</br>


## 安装hexo
```
$ sudo npm install -g hexo-cli
$ sudo npm install -g hexo 
```

# hexo 基础使用

## 建站
```
$ hexo init <folder>    #创建或初始化你的博客项目，存放hexo博客的源码，文件夹的名称可以自定义
$ cd <folder>           #需要注意后面的指令都需要在项目目录下执行
$ npm install           #npm会根据你在package.json中对各种依赖的定义去安装这些依赖
$ hexo g                #生成静态网页文件
$ hexo server           #建立本地的网站
```
完成了以上的命令后就可以在浏览器中登录localhost:4000对博客进行本地调试了
</br>

## 新建文章
```
$ hexo new "<name>"
```
新建了文章后，可以在项目的source/_post目录下看到一个新的文件"name.md"。打开可以看到已经自动生成了header信息，例如：
```
---
title: Hexo 个人博客搭建
tags:
  - Hexo
  - Github
  - 教程
categories:
  - 分类
date: 2021-11-18 17:22:43
---
```
header信息是文章的属性，采用统一的yaml格式，用三条短横线分隔。</br>

| 属性        | 描述         | 默认值      |
| ----------- | ----------- | ----------- |
| layout      | layout类型  | post或者page |
| title       | 文章标题     |  |
| date        | 创建日期     | 文件的创建日期 |
| updated     | 修改日期     | 文件的修改日期 |
| comments    | 是否开启评论  | true   |
| tags  v     | 标签         |        |
| categories  | 分类         |        |
| permalink   | url中的名字  | 文件名  |



我们可以在下面开始编辑文章正文。正文是使用markdown格式，文章编辑时建议使用vs code或其他支持markdown实时查看的工具。

## 草稿
```
$ hexo new draft "<name>"
```
新建草稿后会在source/_draft目录下生成一个name.md文件。这个文件一般是不在网页上显示，链接也无法访问的。
也就是说如果你想把某一篇文章移除显示，又不舍得删除，可以把它移动到_drafts目录之中。
如果你希望强行预览草稿，需要修改项目根目录的_config文件：
```
render_drafts: true
```
或者在本地网址中浏览：
```
$ hexo server --drafts
```

可以通过以下命令将草稿变成文章或者网页：
```
$ hexo publish [layout] <filename>
```

# hexo指令
hexo 指令可以查看官方文档：https://hexo.io/zh-cn/docs/commands.html </br>
这里只记录几个最常用的指令。</br>
## init
```
$ hexo init [folder]
```
新建一个网站。如果没有设置 folder ，Hexo 默认在目前的文件夹建立网站。

## new
```
$ hexo new [layout] <title>
```
新建一篇文章。如果没有设置 layout 的话，默认使用 _config.yml 中的 default_layout 参数代替。如果标题包含空格的话，请使用引号括起来。

## generate
```
$ hexo generate
```
生成静态文件,保存在项目的public文件夹中。
该命令也能简写为：
```
$ hexo g
```

## publish
```
$ hexo publish [layout] <filename>
```
发表草稿

## server
```
$ hexo server
```
启动服务器。默认情况下，访问网址为： http://localhost:4000/。

## deploy
```
$ hexo deploy
```
部署网站,可以将网站部署到github上。

## clean
```
hexo clean
```
清除缓存文件 (db.json) 和已生成的静态文件 (public)。

在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。





