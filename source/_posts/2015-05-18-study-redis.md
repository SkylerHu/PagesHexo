---
title: Linux(SUSE)Redis服务的安装使用
date: 2015-05-18 22:42
tags:
  - Linux
  - Redis
---
### 1、下载源码，解压缩后编译源码。

``` bash
tar -zxzf redis-2.8.3.tar.gz
cd redis-2.8.3
make && make install
```

### 2、编译完成后
有四个可执行文件redis-server、redis-benchmark、redis-cli和redis.conf。然后拷贝到一个目录下。

``` bash
mkdir /usr/redis
cp src/redis-server  /usr/redis
cp src/redis-benchmark /usr/redis
cp src/redis-cli  /usr/redis
cp redis.conf  /usr/redis
```

### 3、配置Redis

	vim /usr/redis/redis.conf

可参考<http://blog.csdn.net/java2000_wl/article/details/8520593>

按如下方式修改相关参数：

```xml
端口默认：6379
daemonize yes
timeout 300
tcp-keepalive 60
```

### 4、启动Redis服务

``` bash
cd /usr/redis
./redis-server redis.conf
```

### 5、然后用客户端测试一下是否启动成功

``` bash	
$ ./redis-cli
$ redis> set “test” “testredis”
$ OK
$ redis> get “test”
$ " testredis "
```

### 6.java客户端的使用

jar包下载地址：<http://download.csdn.net/detail/kuailebeihun/8714073>

```Java
import java.util.HashMap;  
import java.util.Map;  
import java.util.Set;  
import java.util.logging.Logger;  
  
import play.Play;  
  
import redis.clients.jedis.Jedis;  
import redis.clients.jedis.JedisPool;  
import redis.clients.jedis.JedisPoolConfig;  
  
/** 
 * Redis工具类,用于获取RedisPool. 参考官网说明如下： You shouldn't use the same instance from different threads because you'll have strange errors. And sometimes creating lots of Jedis instances is not good enough because it means lots of sockets and connections, which leads to strange errors as well. A single Jedis instance is not threadsafe! To avoid these problems, you should use JedisPool, which is a threadsafe pool of network connections. This way you can overcome those strange errors and achieve great performance. To use it, init a pool: JedisPool pool = new JedisPool(new JedisPoolConfig(), "localhost"); You can store the pool somewhere statically, it is thread-safe. JedisPoolConfig includes a number of helpful Redis-specific connection pooling defaults. For example, Jedis with JedisPoolConfig will close a connection after 300 seconds if it has not been returned. 
 *  
 * @author bqwang 
 */  
public class JedisUtil {  
    /** 
     * 私有构造器. 
     */  
    private JedisUtil() {  
  
    }  
  
    /** 
     * 获取连接池. 
     *  
     * @return 连接池实例 
     */  
    private static JedisPool getPool(String ip, int port) {  
        JedisPool pool = null;  
        JedisPoolConfig config = new JedisPoolConfig();  
        config.setMaxIdle(Integer.parseInt(Play.configuration.getProperty("redis.pool.maxIdle")));  
        config.setMaxTotal(Integer.parseInt(Play.configuration.getProperty("redis.pool.maxTotal")));  
        config.setTestOnBorrow(Play.configuration.getProperty("redis.pool.testOnBorrow") == "true" ? true : false);  
        config.setTestOnReturn(Play.configuration.getProperty("redis.pool.testOnReturn") == "true" ? true : false);  
        try {  
            /** 
             * 如果你遇到 java.net.SocketTimeoutException: Read timed out exception的异常信息 请尝试在构造JedisPool的时候设置自己的超时值. JedisPool默认的超时时间是2秒(单位毫秒) 
             */  
            pool = new JedisPool(config, ip, port, Integer.parseInt(Play.configuration.getProperty("redis.pool.timeout")));  
        } catch (Exception e) {  
            System.out.println("get pool:" + e.getMessage() + "==>" + e.getCause().getMessage());  
            e.printStackTrace();  
        } finally {  
            System.out.println("get pool 获取连接池.....");  
        }  
        return pool;  
    }  
  
    /** 
     * 类级的内部类，也就是静态的成员式内部类，该内部类的实例与外部类的实例 没有绑定关系，而且只有被调用到时才会装载，从而实现了延迟加载。 
     */  
    private static class RedisUtilHolder {  
        /** 
         * 静态初始化器，由JVM来保证线程安全 
         */  
        private static JedisUtil instance = new JedisUtil();  
    }  
  
    /** 
     * 当getInstance方法第一次被调用的时候，它第一次读取 RedisUtilHolder.instance，导致RedisUtilHolder类得到初始化；而这个类在装载并被初始化的时候，会初始化它的静 态域，从而创建RedisUtil的实例，由于是静态的域，因此只会在虚拟机装载类的时候初始化一次，并由虚拟机来保证它的线程安全性。 这个模式的优势在于，getInstance方法并没有被同步，并且只是执行一个域的访问，因此延迟初始化并没有增加任何访问成本。 
     */  
    public static JedisUtil getInstance() {  
        return RedisUtilHolder.instance;  
    }  
  
    /** 
     * 获取Redis实例. 
     *  
     * @return Redis工具类实例 
     */  
    public Set<String> getJedisData(String key) {  
        Set<String> resultSet = null;  
        JedisPool pool = null;  
        Jedis jedis = null;  
        String ip = Play.configuration.getProperty("redis.server.ip");  
        int port = Integer.parseInt(Play.configuration.getProperty("redis.server.port"));  
        // int retryTimes = Integer.parseInt(Play.configuration.getProperty("redis.pool.retryTimes"));  
        // int count = 0;  
        // count++;  
        try {  
            pool = getPool(ip, port);  
            jedis = pool.getResource();  
            resultSet = jedis.smembers(key);  
            // 清空redis中的消息  
            jedis.del(key);  
        } catch (Exception e) {  
            System.out.println("get jedis:" + e.getMessage() + "==>" + e.getCause().getMessage());  
            e.printStackTrace();  
        } finally {  
            // 销毁对象  
            System.out.println("get Jedis ++++++ 销毁对象");  
            if (pool != null) {  
                pool.returnBrokenResource(jedis);  
            }  
        }  
        return resultSet;  
    }  
}
```
