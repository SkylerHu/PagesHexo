---
title: Linux(SUSE) NFS mount挂载
date: 2015-01-01 08:55
tags:
  - NFS
---
被mount的机器（服务机）10.10.10.209

#### 1 创建共享文件夹/data/attachment
 
#### 2 修改/etc/exports，加入

/data/attachments 10.10.10.207(rw,no_root_squash,sync,insecure)

多个服务器如下：

/data/attachments 10.10.10.207(rw,no_root_squash,sync,insecure) 10.10.10.208(rw,no_root_squash,sync,insecure)

 
#### 3 重启相关服务（顺序按下面的进行）

``` bash
service rpcbind stop
service nfsserver stop
service rpcbind start
service nfsserver start
```
 
mount的机器（客户机）10.10.10.207

* 1 创建文件夹/data/attachment
 
* 2 修改/etc/fstab，加入
10.10.103.209:/data/attachments /data/attachments    nfs    defaults       0 0
 
* 3 输入mount -a 使其生效
 
* 4 测试-->输入：showmount -e 10.10.103.209，查看本机的共享目录
如果输出：
Export list for 10.10.103.209:
/data/attachments *
则测试成功

#### 整体测试
在服务机/data/attachment下创建文件，如果在客户机下同样目录下出现次文件，说明成功
 
参考资料

* <http://www.linuxidc.com/Linux/2014-01/95507.htm>
* <http://blog.csdn.net/liangziyisheng/article/details/10705449>
* <http://blog.csdn.net/demo_deng/article/details/9568927>
* <http://www.2cto.com/os/201201/116103.html>
