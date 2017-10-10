---
title: 为终端Terminal设置Shadowsocks代理
date: 2016-12-16 12:54:36
tags:
  - Linux
  - Shadowsocks
---
Shadowsocks是我们常用的代理工具，它使用socks5协议，而终端很多工具目前只支持http和https等协议，对socks5协议支持不够好，所以我们为终端设置shadowsocks的思路就是将socks协议转换成http协议，然后为终端设置即可。仔细想想也算是适配器模式的一种现实应用吧。

想要进行转换，需要借助工具，这里我们采用比较知名的polipo来实现。polipo是一个轻量级的缓存web代理程序。闲话休叙，让我们开始动手吧。

[Shadowsocks的安装与使用](http://pages.skylerhu.com/Shadowsocks-SwitchySharp/)参考链接，点击访问

以下我是在Mac上配置的方式，其他系统可模仿。

#### 安装

```bash
brew install polipo  # brew没安装的同学自行安装
```

#### 修改配置

设置每次登陆启动polipo
```bash
ln -sfv /usr/local/opt/polipo/*.plist ~/Library/LaunchAgents
```

修改文件`/usr/local/opt/polipo/homebrew.mxcl.polipo.plist`设置parentProxy
增加了 `<string>socksParentProxy=localhost:1080</string>`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>homebrew.mxcl.polipo</string>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/opt/polipo/bin/polipo</string>
        <string>socksParentProxy=localhost:1080</string>
    </array>
    <!-- Set `ulimit -n 20480`. The default OS X limit is 256, that's
         not enough for Polipo (displays 'too many files open' errors).
         It seems like you have no reason to lower this limit
         (and unlikely will want to raise it). -->
    <key>SoftResourceLimits</key>
    <dict>
      <key>NumberOfFiles</key>
      <integer>20480</integer>
    </dict>
  </dict>
</plist>
```

#### 启动

```bash
launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.polipo.plist
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.polipo.plis
```

#### 验证

两个语句执行的结果，显示ip地址不一样
```shell
curl ip.gs
http_proxy=http://localhost:8123 curl ip.gs
```

#### 使用

编辑`~/.bash_profile`设置别名, 编辑之后执行`source ~/.bash_profile`
```shell
alias use_ss="export http_proxy=http://localhost:8123 && export https_proxy=http://localhost:8123"
```

执行命令`use_ss`就可以使用了, 根据之下命令执行结果可验证
```bash
curl ip.gs
use_ss
curl ip.gs
```

本文编写[参考](http://droidyue.com/blog/2016/04/04/set-shadowsocks-proxy-for-terminal/index.html)
