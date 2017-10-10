---
title: Linux(SUSE) nginx反向代理配置，负载均衡
date: 2015-01-01 09:04
tags:
  - Linux
  - nginx
---
### 1. 安装jdk

sudo rpm -ivh jdk-7u71-linux-x64.rpm

设置环境变量：
vim /etc/profile

在底部加入：

**$set java environment**

``` bash
JAVA_HOME=/usr/java/jdk1.7.0_71
CLASSPATH=.:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH
```

	vim /etc/profile.d/java.sh

加入：

**$set java environment**

``` conf
JAVA_HOME=/usr/java/jdk1.7.0_71
CLASSPATH=.:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH
```

### 2. 安装包准备

下载地址：<http://download.csdn.net/detail/kuailebeihun/8318449>

``` bash
tar -zxvf 
tar -zxvf nginx-1.7.8.tar.gz
tar -zxvf openssl-1.0.0o.tar.gz 
tar -zxvf zlib-1.2.8.tar.gz 
```

先下载好zlib和openssl
编译nginx时，联合编译zlib和openssl


### 3. 编译安装zlib

``` bash
cd zlib-1.2.8/
./configure
make
make install
```

### 4. 编译安装openssl

``` bash
cd openssl-1.0.0o/
./config
make && make install
```

### 5. 编译安装pcre

``` bash
cd pcre-8.12/
./configure
make && make install
```

### 6. 联合编译安装nginx

``` bash
$ cd nginx-1.7.8
$ ./configure --user=nobody --group=nobody --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/data/moa/pcre-8.12 --with-openssl=/data/moa/openssl-1.0.0o
$ make
$ make install
```

vi objs/Makefile

	查找configure --disable-shared,在1158行,删除./configure --disable-shared, 保存
    	:st nu可显示行号
        :1089直接跳到1158行，:/configure可以搜索
    
    make && make install

### 7. nginx配置
	
	cd /usr/local/webserver/nginx/

配置
	
	vim conf/nginx.conf

``` conf
gzip  on;
	upstream  wplay {
	server 192.168.11.6:80;
}
location / {
	proxy_pass http://wplay;
	proxy_redirect     off;
	proxy_set_header   Host             $host;
	proxy_set_header   X-Real-IP        $remote_addr;
	proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
	proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
	proxy_max_temp_file_size 0;
	proxy_connect_timeout      90;
	proxy_send_timeout         90;
	proxy_read_timeout         90;
	proxy_buffer_size          4k;
	proxy_buffers              4 32k;
	proxy_busy_buffers_size    64k;
	proxy_temp_file_write_size 64k;
}
```

### 8.启动、重启命令

``` bash
cd ..
cd sbin/
./nginx
```

重启
	
	nginx -s reload

### 9. 参考资料

* <http://blog.csdn.net/superbirds/article/details/7290504>
* <http://www.bananawolf.com/html/2012/09/831.html>

