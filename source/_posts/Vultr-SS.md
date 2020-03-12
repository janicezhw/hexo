---
title: Vultr+SS
date: 2019-06-15 11:18:04
tags:
---
记录一下 Vultr VPS + SS配置步骤 
使用环境 Mac,CentOS,Python
<!-- more -->

> 
参考文章：
http://daipei.me/posts/cross_firewall_with_digitalocean_shadowsocks/
https://www.vultrblog.com/vultr-ss.html
[新手教程 ](https://medium.com/@jackme256/%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91-vultr-vps-%E6%90%AD%E5%BB%BA-shadowsocks-ss-%E6%95%99%E7%A8%8B-%E6%96%B0%E6%89%8B%E5%90%91-968613081aae)

## 购买Vultr
[Vultr](https://www.vultr.com/)，注册账号并充值（当时充值有赠送$50，一个月有效期，所以大胆尝试，不用在乎费用）
*（关于费用：Vultr 是按小时计费的，只有服务器空间分配好，才开始按小时计费。所以尝试的费用可以忽略不计）*
### 充值：
支持多种支付方式
![-w766](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15605706239682.jpg)


### 新增服务节点
![-w1219](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15605706576082.jpg)

![-w526](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15605709484524.jpg)

* 服务器地址选择的是New York节点（建议$3.5/month ，2.5美元的只支持ipv6)
* 系统选择CentOS
* 其他默认就行，不用特别设置

点击Deploy Now，就可以了。几分钟后服务器空间就分配好了。

**服务器创建好后，建议先使用ping工具测试一下，如果ip在海外能ping通，国内不行（具体原因大家懂的），则直接新建一个VPS，直到都可以ping通才说明可用。保留可用的vps，其他删除**
> ping工具：
> http://ping.chinaz.com/ 
> http://ping.pe/

## 配置VPS
Mac自带ssh命令
打开终端，ssh登录，输入服务器的ip，密码 ，完成ssh访问：
```
$ ssh root@[your vps ip address]
```

### 配置防火墙
服务器分配的空间自带firewalld防火墙，不用自己安装，如果没有则`$ yum install firewalld firewall-config`通过这个命令进行安装。

* 启动防火墙

```
$ systemctl start firewalld
```
如果后续步骤完成SS配置后，无法上网，可能是防火墙导致的，简单验证方法：先关闭防火墙，测试如果可以上网，则是防火墙没有正常配置SS端口造成的，配置方法查看下节。

* 关闭防火墙

```
$ systemctl stop firewalld
```

### 安装shadowsocks
> ***2019-10-16 更新***
> vulter 现在创建 CentOS系统 为CentOS 8, 默认环境有一些变化，后续步骤与之前不太一样，整理如下：
CentOS 8 系统默认python环境为python3，

* 查看python 环境

```
$ python3 --version
```
如果有显示版本号，则说明Vultr的CentOS 已经有python环境，则我们通过python安装shadowsocks。

* 安装pip

```
$ yum install python3-pip
```

* 安装shadowsocks

```
$ pip3 install shadowsocks
```



> **旧版本 centOS 7**
> * 查看python 环境
> ```
$ python --version
```
> 如果有显示版本号，则说明Vultr的CentOS 已经有python环境，则我们通过python安装shadowsocks。
> * 安装pip
> ```
$ yum install m2crypto python-setuptools
$ easy_install pip
```
> * 安装shadowsocks
> ```
$ pip install shadowsocks
```

* 创建并编辑ss配置文件


```
$ vi /etc/shadowsocks.json
```
按照格式输入一下内容："server"需要替换服务器ip，"server_port"端口也可以更换，"password"设置密码(不能使用默认密码，后面步骤会报错)，然后 :wp保存并退出
```
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

多用户配置（可选，有效性暂未验证) 
把上面"password"替换为"port_password"，如下的设置，则开启多用户配置
```
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "port_password": {
        "8381": "password1",
        "8382": "password2",
        "8383": "password3",
        "8384": "password4"
    }，
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```
* 后台启动ss

```
$ ssserver -c /etc/shadowsocks.json -d start    // 启动
$ ssserver -c /etc/shadowsocks.json -d stop     // 停止
$ ssserver -c /etc/shadowsocks.json -d restart  // 重启
```
> 如遇到 shadowsocks 启动报错：
> undefined symbol: EVP_CIPHER_CTX_cleanup
> 
> xxx/site-packages/shadowsocks/crypto/openssl.py 路径  vim  修改里面的cleanup ->reset

* 防火墙添加shadowsocks端口


```
$ firewall-cmd --add-port=8388/tcp --permanent
```

### 让shadowsocks开机自启
* 安装supervisor

```
<!--CentOS 8-->
$ pip3 install supervisor 

<!--CentOS 7-->
$ easy_install supervisor
```

* 创建配置文件

```
$ echo_supervisord_conf > /etc/supervisord.conf
```

* 修改配置文件

```
$ vi /etc/supervisord.conf
```
在文件末尾加上：
```
[program:ssserver]
command = ssserver -c /etc/shadowsocks.json
autostart=true
autorestart=true
```
* 设置supervisor开机自启

```
$ vi /etc/rc.local
```
在文件末尾加上：
```
supervisord -c /etc/supervisord.conf
```

* 修改权限

```
$ chmod 777 /etc/rc.local
```
这样ss开启自动后台运行就配置好了，服务器重启就会自启动ss服务。

### 安装BBR（可选）
* CentOS 8 默认已经安装BBR, `lsmod | grep bbr` 验证 

> BBR是来自于Google的黑科技，目的是通过优化和控制TCP的拥塞，充分利用带宽并降低延迟，起到神奇般的加速效果。 在BBR之前，比较有名的就是国产的锐速了，不过，锐速是个国产的闭源软件，而BBR则是Google开源软件。

执行下面命令安装谷歌BBR：

```
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```
按照提示完成安装

```
Info: The system needs to reboot.
Do you want to restart system? [y/n]y
```
提示重启VPS，输入y重启，几分钟后，再次终端ssh接入VPS，
重启后输入`lsmod | grep bbr` ，出现 tcp_bbr 即说明 BBR 已经启动。

经测试，测速软件差别不大，但是实际使用YouTube视频，没有BBR时候加载很慢，配置BBR后，视频加载大幅加快。

## shadowsocks客户端

https://shadowsocks.org/en/download/clients.html

> 推荐在GitHub下载对应版本：
> https://github.com/shadowsocks 

* 安卓：[shadowsocks-android](https://github.com/shadowsocks/shadowsocks-android/releases)
* Win：[shadowsocks-windows](https://github.com/shadowsocks/shadowsocks-windows/releases)
* Mac：[ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG/releases)
* iOS：没有官方的客户端，shadowrocket请参看这个帖子https://www.itren.org/100.html 有时候装上会需要输入Appid等，可以在看看这个[帖子](https://github.com/shadowrocketHelp/help/wiki/%E5%9B%BD%E5%A4%96-appstore-id-%E8%B4%A6%E5%8F%B7%E5%88%86%E4%BA%AB)


![-w557](https://dev.tencent.com/u/jindouyun_wk/p/MarkdownImages/git/raw/master/15605829842739.jpg)
