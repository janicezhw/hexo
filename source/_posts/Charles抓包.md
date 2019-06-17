---
title: Charles抓包
date: 2019-05-31 17:22:55
tags:
---
![](https://blog.devtang.com/images/charles-logo.png)

<!-- more -->

## 安装
下载安装[Charles](https://xclient.info/s/charles.html)，安装完成为30天试用版。
帮助 - 注册激活 （激活码SN.txt）
激活完成后打开Charles

## 配置
第一次打开Charles，会请求设置系统代理权限，可以忽略（好像是Mac抓包的情况需要用到，未验证）
### Charles配置
要截取 iPhone 上的网络请求，我们首先需要将 Charles 的代理功能打开。在 Charles 的菜单栏上选择 “Proxy”->”Proxy Settings”，填入代理端口 8888，并且勾上 “Enable transparent HTTP proxying” 就完成了在 Charles 上的设置。如下图所示:
![](https://blog.devtang.com/images/charles-proxy-setting.jpg)

### 在iPhone上配置
首先获取MAC 的ip地址： Charles 的顶部菜单的 “Help”->”Local IP Address”，：
iPhone连接同一个网络，并设置手动代理
![](https://blog.devtang.com/images/charles-iphone-setting.jpg)

设置完成后，在Charles 会弹出授权请求，点击“Allow”

这样就完成了http抓包设置

## 抓包Https
要抓包Https通讯信息，还需要配置SSL相关证书
### 首先Mac电脑上安装Charles CA证书
首先我们需要在 Mac 电脑上安装证书。点击 Charles 的顶部菜单，选择 “Help” -> “SSL Proxying” -> “Install Charles Root Certificate”
Mac的kechain 将会添加一个Charles Proxy CA证书，通常这时候证书会显示为不受信任，双击证书，如下操作信任证书
![-w350](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15595433607741.jpg)


### iPhone上安装Charles描述文件
如果我们需要在 iOS 或 Android 机器上截取 Https 协议的通讯内容，还需要在手机上安装相应的证书。点击 Charles 的顶部菜单，选择 “Help”__ -> “SSL Proxying” -> “Install Charles Root Certificate on a Mobile Device or Remote Browser”，然后就可以看到 Charles 弹出的简单的安装教程![](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15595433914976.jpg)
安装描述文件
![](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15595434048643.jpg)


备注：在iOS10+系统，会需要信任证书：iPhone “设置”-“关于本机”-“证书信任设置”，信任Charles证书。

### 截取对应域名的Https通讯信息
Charles 默认并不截取 Https 网络通讯的信息，如果你想对截取某个网站上的所有 Https 网络请求，可以相应域名 右键“启用SSL代理”
![](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15595434132822.jpg)


> 参考文章：
> https://blog.devtang.com/2015/11/14/charles-introduction/