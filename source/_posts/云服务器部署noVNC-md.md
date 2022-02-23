---
title: 云服务器部署noVNC
date: 2022-02-22 16:43:24
tags: 
- 服务器
- VNC
- 教程
---

受到百度apollo的实验平台的启发，想要搭建一个自己的实验平台，可以实现在浏览器中通过账号访问指定的服务器并进行实验的功能。</br>
本篇只记录novnc的搭建过程。
系统：Ubuntu 18.04

<!-- more -->

# 购买云服务器
由于需要用过公网IP来访问服务器，所以需要购买一个云服务器。这次使用了[腾讯云](https://cloud.tencent.com/)的服务器，只需要38块就可以购买一个“1核50G/年”的服务器，特别适合用来练手。

{% asset_img tencent-1.jpg tencent cloud %}
每个账号首次购买才有这个优惠。

# 重装系统（Ubuntu 18.04）
点击腾讯云官网的控制台->轻量应用服务器。

{% asset_img tencent-2.jpg tencent cloud %}

然后点击更多->管理,进行云服务器的管理界面

{% asset_img tencent-3.jpg tencent cloud %}

在管理界面中找到镜像信息卡片，点击重装系统

{% asset_img tencent-4.jpg tencent cloud %}

点击“官方系统”->“系统镜像”->“Ubuntu 18.04 LTS”，然后点击确定进行系统重装

{% asset_img tencent-5.jpg tencent cloud %}

等待几分钟后系统完成重装。然后点击服务器管理界面右上方的重置密码按钮进行设置密码。

{% asset_img tencent-6.jpg tencent cloud %}
输入密码后点击下一步，系统会强制重启服务器，重启完成后就可以登录自己的云服务器了。

# 安装可视化桌面
安装镜像后默认是一个服务器的Ubuntu系统，为了方便使用，我们为其安装一个桌面。

在服务器管理界面中的远程登录卡片中点击“一键登录”

{% asset_img tencent-7.jpg tencent cloud %}

然后输入
```
$ sudo tasksel
```
{% asset_img tencent-8.jpg tencent cloud %}

找到Lubuntu Desktop,然后按下空格键选中，再点击tab键，再点击回车键进行安装。
注：这里可以安装其他的桌面，比如Kubuntu、XUbuntu等，但是考虑到购买的服务器性能较低，推荐使用Lubuntu。

安装完成后，回到服务器管理界面点击重启按钮。重启完成后点击远程登录卡片中的“VNC登录”，就可以看到桌面系统了。

{% asset_img tencent-9.jpg tencent cloud %}

点击下拉箭头->other,然后输入服务器的账号和密码即可登录。

{% asset_img tencent-10.jpg tencent cloud %}

# 安装vncserver
输入以下命令安装并启动vncserver：

```
$ sudo apt-get install tightvncserver
$ vncserver :1
```
需要注意的是执行“vncserver :1”设置为1时，下面要运行noVNC时的端口号为5900 + 1，就是5901。

# 下载noVNC
执行以下命令下载noVNC项目：
```
$ git clone git://github.com/kanaka/noVNC
```

# 创建安全连接
VNC的默认会话不是安全的，我们需要创建一个安全的VNC连接：
```
$ cd noVNC/utils/
$ openssl req -new -x509 -days 365 -nodes -out self.pem -keyout self.pem
```

# 运行noVNC
```
$ cd ~/noVNC/utils/
$ ./novnc_proxy --vnc localhost:5901
```
{% asset_img tencent-11.jpg tencent cloud %}

注：将localhost修改为云服务器的公网IP即可通过其他设备进行访问

运行成功后可以看到一个链接地址：
```
http:/localhost:6080/vnc.html?host=localhost&port=6080
```

# 修改防火墙
由于noVNC将5901端口代理为6080，我们只要将防火墙添加一个自定义开放6080端口。

{% asset_img tencent-12.jpg tencent cloud %}

点击添加规则，端口设置为6080，策略设置为允许即可。

# 用浏览器登录云服务器

在电脑上打开浏览器，然后输入链接即可登录云服务器
{% asset_img tencent-13.jpg tencent cloud %}
