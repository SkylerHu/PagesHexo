---
title: MQTT mosquitto的初步学习
date: 2014-05-07 22:44
tags:
  - mosquitto
  - MQTT
---
### mosquitto简介

MQTT（MQ Telemetry Transport），消息队列遥测传输协议，轻量级的发布/订阅协议，适用于一些条件比较苛刻的环境，进行低带宽、不可靠或间歇性的通信。
Mosquitto是一个开源(BSD许可证)的消息代理，实现MQTT(消息队列遥测传输)协议版本3.1。

为每个MQTT消息头命令消息包含一个固定头，头只有两个字节，格式如下：

![消息头](http://7xpdkx.com1.z0.glb.clouddn.com/2016/02/22/21c8ff27db.png)

参考：<http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/mqtt-v3r1.html#msg-format>

## 一、安装mosquitto服务
本文仅介绍mosquitto在Windows上的安装，Linux系统与之类似

在mosquitto官网下载安装包<http://mosquitto.org/files/binary/win32/mosquitto-1.2.3-install-win32.exe>
下载之后直接安装即可

## 二、配置和运行
可参照<http://www.cnblogs.com/li-baibo/archive/2013/01/21/2869225.html>

## 三、在客户端的使用（Java）
pc 端客户端，下载地址：<http://download.csdn.net/detail/kuailebeihun/7312947>
解压，运行\ia92\J2SE\wmqttSample.jar即可
### 1. 客户端使用的库
下载地址：<http://download.csdn.net/detail/kuailebeihun/7312731>（JDK环境1.6及以上，若以下请下载源码开发）
### 2. 客户端 库 的源码 
下载地址：<http://download.csdn.net/detail/kuailebeihun/7312743>
### 3.  具体使用
#### （1）消息质量
0： “至多一次”，消息发布完全依赖底层 TCP/IP 网络。会发生消息丢失或重复。这一级别可用于如下情况，环境传感器数据，丢失一次读记录无所谓，因为不久后还会有第二次发送；
1 ：“至少一次”，确保消息到达，但消息重复可能会发生；
2：“只有一次”，确保消息到达一次。这一级别可用于如下情况，在计费系统中，消息重复或丢失会导致不正确的结果。

#### （2）消息主题

构建一个应用程序时,主题树的设计应考虑以下主题名称的语法和语义的原则:
主题必须至少一个字符长；　　
  主题名称是区分大小写的，例如,A和a是两个不同的主题；
  “/”创造了一个独特的主题，例如,/a与a是不同的主题。/a匹配“+/+”和“/+”,但不匹配“+”；　
  任何主题不包含空字符(Unicode \ x0000)。　　
 
  以下原则适用于主题树的结构和内容: 　　　　
  64 k的长度是有限的,但在没有限制水平主题树的数量。　　
  可以有任意数量的根节点,也就是说,可以有任意数量的主题树。

#### (3) subscribe订阅

使用正斜杠(/)分隔主题树中的每个级别,并提供一个主题空间的层次结构。主题层面分离器的使用中遇到两个通配符时重要的主题由用户指定。

数字符号(#)是一个通配符匹配任意数量的水平在一个主题，只能用在最后，如a/#/c是不合法的
例如,如果你订阅a/b/c/#,你在这些主题接收消息：
a/b/c
a/b/c/d
a/b/c/e


加号(+)是一个通配符匹配的主题只有一个水平
例如,如果你订阅a/+/c/#,你在这些主题:接收消息：
 a/a/c/
a/b/c/d
a/c/c/e

**若订阅"+/#"，此主题可接受所有类型主题的消息**

#### (4) publish发布
发布的时候主题时，+或者#不能通配，是且仅是一个明确的主题。


**<span style="clolor: #ff0000;">贴上一段简单代码</span>**

```Java
import org.eclipse.paho.client.mqttv3.MqttCallback;  
import org.eclipse.paho.client.mqttv3.MqttClient;  
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;  
import org.eclipse.paho.client.mqttv3.MqttDeliveryToken;  
import org.eclipse.paho.client.mqttv3.MqttException;  
import org.eclipse.paho.client.mqttv3.MqttMessage;  
import org.eclipse.paho.client.mqttv3.MqttSecurityException;  
import org.eclipse.paho.client.mqttv3.MqttTopic;  
import org.eclipse.paho.client.mqttv3.internal.MemoryPersistence;  
  
/**  
 *  
 * @author LP by 2014-04-24 
 * 
 */  
public class MqttServiceClient implements MqttCallback {  
  
    private static final String MQTT_HOST = "tcp://192.168.12.38:1883";  
    private static final String MQTT_CLIENT = "Test_";  
      
    public static MqttServiceClient mqttServiceClient = null;  
      
    private MqttClient client = null;  
    private MqttConnectOptions options = null;  
      
    /** 
     * 单例模式构造类 
     */  
    public static MqttServiceClient getInstance() {  
        if (mqttServiceClient == null) {  
            mqttServiceClient = new MqttServiceClient();  
        }  
        return mqttServiceClient;  
    }  
  
    private MqttServiceClient() {  
        System.out.println("init MQTTClientService");  
        init();  
    }  
    // The major API implementation follows :-  
  
    /** 
     * 初始化 
     */  
    private void init() {  
        try {  
          
            // host为主机名，test为clientid即连接MQTT的客户端ID，一般以客户端唯一标识符表示，MemoryPersistence设置clientid的保存形式，默认为以内存保存  
            client = new MqttClient(MQTT_HOST, MQTT_CLIENT, new MemoryPersistence());  
            // MQTT的连接设置  
            options = new MqttConnectOptions();  
            // 设置是否清空session,这里如果设置为false表示服务器会保留客户端的连接记录，这里设置为true表示每次连接到服务器都以新的身份连接  
            options.setCleanSession(true);  
            // 设置连接的用户名  
            // options.setUserName(userName);  
            // 设置连接的密码  
            // options.setPassword(passWord.toCharArray());  
            // 设置超时时间 单位为秒  
            options.setConnectionTimeout(50);  
            // 设置会话心跳时间 单位为秒 服务器会每隔1.5*20秒的时间向客户端发送个消息判断客户端是否在线，但这个方法并没有重连的机制  
            options.setKeepAliveInterval(30);  
            // 设置回调  
            client.setCallback(this);  
              
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
    /** 
     * 连接到MQTT 
     */  
    void connect() {  
        System.out.println("Start connect----------");  
        try {  
            client.connect(options);  
            //订阅主题的方法，2为消息的质量  
            client.subscribe("+/#", 2);  
            //发送消息  
            publish("test", "撒打发水电费水电费");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
      
    /** 
     * 断开连接到MQTT 
     */  
    public void disconnect() {  
        System.out.println("Start disconnect----------");  
        try {  
            client.disconnect();  
        } catch (MqttSecurityException e) {  
            e.printStackTrace();  
        } catch (MqttException e) {  
            e.printStackTrace();  
        }  
    }  
  
    /**  
     * 发布消息 
     * @param topic 主题 
     * @param msg 消息 
     */  
    public void publish(String topic, String msg) {  
        System.out.println("Start publish----------");  
        try {  
            MqttTopic mqttTopic = client.getTopic(topic);  
            //2为消息的质量  
            MqttDeliveryToken messageToken = mqttTopic.publish(msg.getBytes(), 2, true);  
            System.out.println("publish success==>"+messageToken.getMessage());  
//          client.publish(topic, 2, msg);  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
      
      
// -------------------------------------------------回调方法------------------------------------------------------------//  
      
    /**  
     * 连接断开触发此方法 
     */  
    @Override  
    public void connectionLost(Throwable cause) {  
        System.out.println("Connection Lost---------->" + cause.getMessage());  
    }  
  
    /**  
     * 消息达到触发此方法 
     */  
    @Override  
    public void messageArrived(MqttTopic topic, MqttMessage message)  
            throws Exception {  
        System.out.println(topic + ":" + message.toString());  
    }  
  
    /** 
     * 消息发送成功触发此方法 
     */  
    @Override  
    public void deliveryComplete(MqttDeliveryToken token)  {  
        try {  
            System.out.println("deliveryComplete---------" + token.getMessage());  
        } catch (MqttException e) {  
            e.printStackTrace();  
        }  
    }  
  
      
    public static void main(String[] args)throws Exception {  
          
        MqttServiceClient.getInstance().disconnect();  
        MqttServiceClient.getInstance().connect();  
          
        new Thread() {  
            public void run() {  
                int count = 0;  
                while(true) {  
                    try {  
                        Thread.sleep(1000*3);  
                    } catch (InterruptedException e) {  
                        e.printStackTrace();  
                    }  
                    MqttServiceClient.getInstance().publish("AAA", "hello world ! count=" + count);  
                    count ++;  
                }  
            };  
        }.start();  
    }  
      
}
```
