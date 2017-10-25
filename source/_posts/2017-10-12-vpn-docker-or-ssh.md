---
title: 使用Docker搭建VPN
date: 2017-10-12 10:57:49
tags: VPN, Liunx
---

> 若要申请VPS可使用这个[DigtalOcean](http://www.digitalocean.com/?refcode=a80753a9aac2)，会赠送你$10用于购买VPS。

#### 通过SSH隧道翻墙
本地执行
```
ssh -D -N 7777 root@host
```
需要使用代理的地方，都设置proxy到 127.0.0.1:7777

浏览器可通过`SwitchyOmega`添加情景方式

    代理协议使用SOCKS5，代理服务器地址127.0.0.1，代理端口7777

### 使用Docker
要说安装，最简洁明了方便当然是试用`Docker`啦

安装Docker, 可根据自己系统参考[官网](https://docs.docker.com/engine/installation/)
```
apt install docker.io  # Ubuntu 16.04为例的
```

#### l2tp

```
docker pull fcojean/l2tp-ipsec-vpn-server
```

VERY IMPORTANT ! First, run this command on the Docker host to load the IPsec NETKEY kernel module:
`sudo modprobe af_key`

vim `/etc/docker/vpn.env`，添加内容
```
VPN_IPSEC_PSK=0819
VPN_USER_CREDENTIAL_LIST=[{"login":"skyler","password":"skyler"},{"login":"guest","password":"guest"}]
VPN_NETWORK_INTERFACE=eth0
```

启动服务
```
docker run \
    --name l2tp-ipsec-vpn-server \
    --env-file /etc/docker/vpn.env \
    -p 500:500/udp \
    -p 4500:4500/udp \
    -v /lib/modules:/lib/modules:ro \
    -d --privileged \
    fcojean/l2tp-ipsec-vpn-server

```

check检测运行状态
```
docker exec -it l2tp-ipsec-vpn-server ipsec status
```

#### Shadowsocks

```
docker pull mritd/shadowsocks
```

启动，可修改`18888`端口及`-m`(加密方式)、`-k`(用户名)的参数
```
docker run -dt --name shadowsocks -p 18888:18888 mritd/shadowsocks -s "-s 0.0.0.0 -p 18888 -m rc4-md5 -k skyler --fast-open"
```

ss不使用Docker也很简单，查看Shadowsocks[官网](http://shadowsocks.org/en/download/servers.html)


### 附Terminal使用代理

vim `~/.zshrc`或者`~/.bashrc`添加
 ```
 # proxy
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:7777"
alias unsetproxy="unset ALL_PROXY"
alias ip="curl -i http://ip.cn"
```

```
source ~/.zshrc
setproxy  # 设置代理
unsetproxy  # 取消代理
ip  # 可用此命令验证
```
