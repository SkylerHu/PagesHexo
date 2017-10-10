---
title: Ubuntu搭建VPN
date: 2016-06-26 18:46:54
tags:
  - VPN
---
### 分享链接：<http://www.digitalocean.com/?refcode=a80754a9aac2>
若要申请VPS可使用这个链接，会赠送你10$费用可以购买VPS。

----

采用PPTP搭建VPN，优点是配置简单快捷。将亲身过程记录下来供亲们参考，并将过程中遇到的问题也一一列举出来解决方式。。。

直接上步骤:

#### 1.第一步需要安装PPTP，以用来提供VPN服务.

``` bash
sudo apt-get install pptpd
```

如果有问题的话比如提示找不到之类的，apt-get update 一下应该就可以了，然后再来一次就会自动完成安装。

#### 2.装好了之后我们需要进行配置一下以让它可以使用.

``` bash
sudo vi /etc/pptpd.conf
```

取消掉以下 2 行的注释：

``` bash
localip 192.168.0.1
remoteip 192.168.0.234-238,192.168.0.245
```

分别是通过VPN连接后主机和客户端所使用的IP，可以自行修改。注意这个IP在下面还会用的到。

#### 3.然后我们需要分配账号给自己使用.

``` bash
sudo vi /etc/ppp/chap-secrets
```

这个是用户列表文件

在里面添加账户按如下格式

``` bash
username  pptpd  "password"  *
```

`username`为你的用户名`password`为你的密码，密码用引号引起,最后的`*`号表示允许在任意IP连接到服务

#### 4.至此服务弄好了
如果你sudo service pptpd restart一下，就应该已经能连接到该VPN了，但是连接了之后会发现还访问不了外网。然后我们需要让他能访问外网。首先，

``` bash
sudo vi /etc/ppp/pptpd-options
```

找到ms-dns，取消掉注释，改成你喜欢的DNS比如8.8.8.8, 8.8.4.4

#### 5.然后我们要开启内核IP转发

``` bash
sudo vi /etc/sysctl.conf
```

取消掉 `net.ipv4.ip_forward=1` 这一行的注释.

``` bash
sysctl -p
```

#### 6.然后我们需要安装iptables，用来实现请求的NAT转发

``` bash
sudo apt-get install iptables
```

然后开启NAT转发.

``` bash
sudo iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
```

192.168.0.0/24是你在上面设置的IP段，让这个段转发

注意eth0是你连接外网的那块网卡，不一定是0也有可能是1或者看你的机器哪块网卡连的外网了。这样就以NAT的方式请求外网的东西了。不知道你的机器哪块网卡连的外网的话ifconfig一下看看哪个网卡是外网IP就知道了。

``` bash
iptables -t nat -A POSTROUTING -s 192.168.217.0/24 -o eth0 -j MASQUERADE
iptables-save > /etc/iptables.pptp
```

只需执行上面的命令，下面的命令可以备用。
``` bash
iptables -F	//清空所有规则
iptables -t nat -vnL POSTROUTING --line-number   //查看nat表
iptables -t nat -D POSTROUTING [num]	//删除nat表中postrouting的第一条规则
```


开启iptables转发

在/etc/network/if-up.d/目录下创建iptables文件，内容如下：

``` bash
\#\!/bin/sh
iptables-restore < /etc/iptables.pptp
```

给脚本添加执行权限：

``` bash
chmod +x /etc/network/if-up.d/iptables
```

####7.最后，我们需要重启服务，让配置生效

``` bash
sudo service pptpd restart
/etc/init.d/pptpd restart
```
