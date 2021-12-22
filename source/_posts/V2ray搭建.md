---
title: V2ray搭建
date: 2021-11-19 22:12:32
tags:
- 科学上网
- 教程
- 服务器
---

# 前言
记录一下自己V2Ray的搭建过程，方便以后回顾。
搭建过程参考了[搭建V2Ray,安装部署图文教程](https://www.itblogcn.com/article/1501.html)，感谢大佬。

<!--more-->

# 购买云主机
V2Ray搭建之前我们先要有一个VPS服务器，常用的服务器有[搬瓦工](https://bwh81.net/)或者[Vultr](https://www.vultr.com/)等，这两家都可以支持支付宝支付，非常方便。</br>
VPS 速度：香港线路 > CN2 GIA 线路 > CN2 线路 > 普通线路</br>
如果没有特别高的带宽和流量需求，可以选择最便宜的套餐。笔者购买的是搬瓦工的服务器，$49.99一年的服务，大概就是320左右一年，实际用起来上github或youtube都挺流畅的。</br>

{% asset_img bwg_order.jpg 选择套餐 %}

之后只要按照指示一直填写信息并使用支付宝支付即可。购买完成后可以在邮箱中收到邮件，按照邮件操作可以获得你的服务器的信息，包括IP、端口号、密码。</br>

# 登录服务器
购买了服务器后使用SSH指令即可远程登录你的主机，在window系统下可以使用[XSHELL软件](https://xshell.en.softonic.com/)链接服务器。</br>

{% asset_img xshell_new.jpg 新建会话 %}

安装并启动Xshell,然后点击文件->新建</br>

{% asset_img xshell_config.jpg 配置参数 %}


| 属性       | 值               | 
| ------     | ------          | 
| 名称       | 任意名称          | 
| 协议       | SSH              | 
| 主机       | 服务器的IP地址    | 
| 端口       | 服务器的端口号    | 


</br>
{% asset_img bwg_user.jpg 设置账号密码 %}

点击左侧的用户身份验证，用户名为“root”（注意不是主机名称），密码为服务器密码，方法设置为password。</br>
设置完成后点击确定，然后可以双击会话登录到自己的服务器了。

{% asset_img bwg_login.jpg 登入服务器 %}

看到终端中的用户名变成服务器的名称代表我们成功登入服务器了。</br>

# V2Ray安装
## 安装wget
```
$ sudo yum -y install wget
```

## 安装unzip
```
$ sudo yum -y install zip unzip
```

## 安装V2Ray
```
 bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```
</br>

# V2Ray配置
## 获取用户id：
```
$ cat /proc/sys/kernel/random/uuid
```
运行这个命令会在终端打印出一串uuid，我们记住这个uuid。

## 配置config.json
```
$ vi /usr/local/etc/v2ray/config.json
```
编辑V2Ray的配置文件，如果服务器没有vim编辑器，执行该指令安装：

```
$ yum install vim
```
将以下信息复制到config.json中，使用xshell可以直接将数据带格式复制到vim编辑器中，非常方便。

```
{
  "inbounds": [{
    "port": 2088,
    "protocol": "vmess",
    "settings": {
      "clients": [
        {
          "id": "uuid",
          "level": 1,
          "alterId": 64
        }
      ]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      }
    ]
  }
}
```
需要注意“port”的值需要记录一下，会在客户端设置时用到。
另外需要将上一步中获取的uuid填写到“uuid”位置中。</br>
配置完成后保存即可。

## 相关命令
执行下面的命令手动启动V2Ray
```
$ sudo systemctl start v2ray
```

其他命令：
```
$ sudo systemctl stop v2ray  # 停止
$ sudo systemctl restart v2ray  # 重启
```

</br>

# V2Ray客户端配置 --WINDOWS

## 下载客户端程序
在[V2RayN Release](https://github.com/2dust/v2rayN/releases)中可以下载各个版本的客户端软件。我们只要下载V2RayN-Core.zip即可。

下载并解压文件后，打开V2Ray.exe。

{% asset_img V2RayN_1.jpg V2RayN界面 %}

点击服务器->添加VMess服务器
{% asset_img V2RayN_2.jpg 添加VMess %}

| 属性       | 值          | 
| ------     | ------     | 
| 地址       | 服务器IP    | 
| 端口       | “config.json”中的端口号     | 
| 用户ID     | 服务器的uuid     | 


## 客户端使用
{% asset_img V2Ray_setting.jpg 设置 %}
鼠标右键点击V2Ray图标->系统代理->自动配置系统代理

V2Ray图标变成红色时表示V2Ray正在工作了。</br></br>
enjoy yourself~~



