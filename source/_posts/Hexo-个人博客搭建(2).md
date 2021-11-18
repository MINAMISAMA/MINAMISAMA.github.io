---
title: Hexo-个人博客搭建（2）
tags:
  - Hexo
  - Github
  - 教程
categories:
  - Hexo
date: 2021-11-19 02:23:54
---
本篇记录如何将Hexo博客部署到github上，然后可以通过浏览器直接访问你的博客。Hexo搭建的系列：
[Hexo 个人博客搭建（1）](https://minamisama.github.io/2021/11/18/Hexo-%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA(1)/)
[Hexo 个人博客搭建（2）](https://minamisama.github.io/2021/11/19/Hexo-%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA(2)/)

<!--more-->

# git安装和配置
在进行git的安装和配置之前，我们需要有一个git账号。没有github账号可以在[GitHub注册](https://github.com/signup)中注册一个账号。
## 安装git和gitcore
```
$ sudo apt-get install git
$ sudo apt-get install git-core
```
</br>

## 设置本机git账号
```
$ git config --global user.name "yourname"
$ git config --global user.email "youremail"   #注册github账户使用的邮箱
```

## 生成密钥
```
$ ssh-keygen -t rsa -C "youremail"
```
执行后生成密钥文件，密钥保存在~/.ssh/目录下,id_rsa文件为你的私钥，id_rsa.pub为你的公钥：
```
.
├── id_rsa
├── id_rsa.pub
└── known_hosts
```

## 设置SSH keys
在github中点击自己的头像，然后点击settings进入设置界面。
{% asset_img SSH_key.jpg SSH_KEY %}
在左侧找到并点击“SSH and GPG keys”，然后点击右侧的“New SSH key”。
</br>
{% asset_img add_key.jpg 添加一个新的密钥 %}

title中可以输入自定义的名称，Key中需要输入~/.ssh/id_rsa.pub文件中的信息。输入完成后点击下面的Add SSH key即完成SSH keys的设置。

我们可以在终端中输入以下指令查看是否设置成功：
```
$ ssh -T git@github.com
```
如果有类似以下的信息则表示设置成功
```
Hi MINAMISAMA! You've successfully authenticated, but GitHub does not provide shell access. 
```

## 创建仓库
{% asset_img new_repositories.jpg 创建新仓库 %}
在github中点击自己的头像，然后点击Your Repositories进入仓库的编辑页面。然后点击“New”创建新的仓库。

{% asset_img create_repositories.jpg 创建新仓库 %}
创建新仓库时需要将仓库名称设置为“name/github.io”，其中name为你自己的github账户名称，由于笔者已经创建了一个同名的仓库，所以图片显示名称错误。</br>
然后需要将此仓库设置为公开的（public），最后点击Create完成创建。

## 同步远程仓库
在网站项目的目录下执行以下几行命令：
```
git init  #初始化git环境
git remote add origin git@github.com:yourName/yourRepo.git  #后面的仓库改为自己的仓库名称
```
输入以上命令后需要按照终端上的指示输入自己的github用户名称和邮箱。

# Hexo部署
笔者几年前的博客由于没有进行源码备份，更换电脑后已经没办法再去编辑了为了防止再次丢失自己的文章，我们可以将源码上传到github上。
由于hexo部署时在github上保存的只有网站的静态文件，为了可以同时实现源码的备份，我们可以通过分支的方式同时在一个项目中同时存放静态文件和源码。

```
git checkout -b Hexo
```
创建一个新的分支“Hexo”
然后编辑网站的根目录下的_config.yml文件，将末尾修改为：
```
deploy:
type: git
repo: https://github.com/YourgithubName/YourgithubName.github.io.git
branch: master
```
repo中输入你的仓库地址，branch中指定你网址的静态文件存放的位置。
branch也可以设置为Hexo，但是这样就需要你将默认分支设置为Hexo，并且源码要上传到master分支。</br>
安装部署命令
```
$ npm install hexo-deployer-git --save
```


最后输入以下指令即可完成部署：
```
hexo g
hexo d
```

部署完成后可以在浏览器中输入YourgithubName.github.io访问你的博客了。
enjoy yourself~

