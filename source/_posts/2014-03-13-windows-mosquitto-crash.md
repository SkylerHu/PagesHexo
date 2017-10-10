---
title: windows mosquitto.exe闪退
date: 2014-03-13 16:31:00
tags:
  - mosquitto
  - MQTT
---
* mosquitto
* mqtt
* 推送


解决方法：
 
计算机-->管理-->服务——>
 
找到Mosquitto Broker，若为启动状态，点击“停止”，然后右键属性设置为手动启动。

若还是不行，则1883端口被占用了。

命令行查询：netstat -ano | findstr 1883

杀掉进程的命令：taskkill /pid 4 /f
其中 /f 表示强制关闭该进程，4是查询结果显示的pid号
