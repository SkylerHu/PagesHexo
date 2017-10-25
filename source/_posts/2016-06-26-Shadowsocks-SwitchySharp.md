---
title: Shadowsocks+SwitchySharp
date: 2016-06-26 18:56:08
tags:
  - Shadowsocks
---

> 若要申请VPS可使用这个[DigtalOcean](http://www.digitalocean.com/?refcode=a80753a9aac2)，会赠送你$10用于购买VPS。

ShadowSocks[官网](http://shadowsocks.org/en/download/servers.html)

#### 1. gcc安装

``` bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-4.8
```

#### 2. go 环境搭建

``` bash
wget https://storage.googleapis.com/golang/go1.4.linux-386.tar.gz
sudo tar -C /usr/src -xzf go1.4.linux-386.tar.gz
```

在 $HOME/.bashrc 文件末添加如下

``` bash
export GOROOT=/usr/src/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=$HOME/go
```

执行

``` bash
source ~/.bashrc

cd $GOROOT/src
sudo ./all.bash       （若是缺少，apt-get install gccgo-go）
```

#### 3. 安装git

``` bash
sudo apt-get install git-core
```

#### 4. 安装

``` bash
go get github.com/shadowsocks/shadowsocks-go/cmd/shadowsocks-server
shadowsocks-go
```

#### 5. 配置

```bash
cd /etc/
sudo mkdir shadowsocks
cd shadowsocks
sudo touch config.json
```

#### 6. 写入

```bash
{
  "server": "0.0.0.0",
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "timeout": 300,
  "method": "aes-256-cfb",
  "fast_open": false,
  "port_password": {
    "12345": "xxx1",
    "12346": "xxx2"
  }
}
```

#### 7. 启动关闭

```bash
cd ~
vim .profile
添加
export PATH="$PATH:/root/go/bin"

source .profile
shadowsocks-server -c /etc/shadowsocks/config.json -d start
shadowsocks-server -c /etc/shadowsocks/config.json -d stop

nohup shadowsocks-server -c /etc/shadowsocks/config.json
```

#### 8. 开机启动

编辑 /etc/rc.local,在exit 0之前

`/root/go/bin/shadowsocks-server -c /etc/shadowsocks/config.json start`

#### 9. 配置SwitchySharp

```bash
(fbcdn|akamaihd|pixnet)\.net|wretch\.cc|t\.co|goo\.gl|(google(usercontent|apis)*|chrome|staticflickr|imdb|ytimg|gstatic|html5rocks|amazonaws|github|tumblr|addthis|wordpress|blogger|(blog|app)spot|friendfeed|twitter|facebook|youtube|dropbox|feedburner|googleapis|android)\.com
```
<http://autoproxy-gfwlist.googlecode.com/svn/trunk/gfwlist.txt> (这个连接404访问不了， 可用下面链接)
<https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt>
