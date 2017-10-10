---
title: JAVA调用https, JS跨域请求，客户端解决JS跨域问题, SOP
date: 2014-11-12 09:25
tags:
  - Web
  - Play
---
`同源协议 (SOP,Same-Origin-Policy)` 可防止从一个来源加载的脚本获取或操纵来自另一来源的文档的属性或方法。术语来源 是域名、应用程序协议和运行脚本文档的端口的结合。可能存在关于 SOP 概念的一些误解；SOP 指只能从站点 A、不能从站点 B 获取信息。您需要知道在 SOP 限制下可以做什么，不可以做什么。



### 1.JAVA https请求
在做微信服务号的时候出现的问题，解决方法：自定义证书管理器。证书管理器的作用就是让它信任我们指定的证书，意味着信任所有证书，不管是否权威机构颁发，证书有了，通用的https请求方法就不难实现了。

```Java
package com.qq.weixin.util;  
import java.io.BufferedReader;  
import java.io.InputStream;  
import java.io.InputStreamReader;  
import java.io.OutputStream;  
import java.net.URL;  
import java.security.cert.CertificateException;  
import java.security.cert.X509Certificate;  
  
import javax.net.ssl.HttpsURLConnection;  
import javax.net.ssl.SSLContext;  
import javax.net.ssl.SSLSocketFactory;  
import javax.net.ssl.TrustManager;  
import javax.net.ssl.X509TrustManager;  
    
/**  
 * 公众平台通用接口工具类  
 *  from==> 
 *      shttp://blog.csdn.net/lyq8479/article/details/9841371 
 * @author liuyq  
 * @date 2013-08-09  
 */    
public class WXHttpUtil {    
      
    /**  
     * 发起https请求并获取结果  
     *   
     * @param requestUrl 请求地址  
     * @param requestMethod 请求方式（GET、POST）  
     * @param outputStr 提交的数据  
     * @return String  
     */    
    public static String httpsRequest(String requestUrl, String requestMethod, String outputStr) throws Exception {    
        StringBuffer buffer = new StringBuffer();    
        // 创建SSLContext对象，并使用我们指定的信任管理器初始化    
        TrustManager[] tm = { new MyX509TrustManager() };    
        SSLContext sslContext = SSLContext.getInstance("SSL", "SunJSSE");    
        sslContext.init(null, tm, new java.security.SecureRandom());    
        // 从上述SSLContext对象中得到SSLSocketFactory对象    
        SSLSocketFactory ssf = sslContext.getSocketFactory();    
          
        URL url = new URL(requestUrl);    
        HttpsURLConnection httpUrlConn = (HttpsURLConnection) url.openConnection();    
        httpUrlConn.setSSLSocketFactory(ssf);    
          
        httpUrlConn.setDoOutput(true);    
        httpUrlConn.setDoInput(true);    
        httpUrlConn.setUseCaches(false);    
        // 设置请求方式（GET/POST）    
        httpUrlConn.setRequestMethod(requestMethod);    
          
        if ("GET".equalsIgnoreCase(requestMethod))    
            httpUrlConn.connect();    
          
        // 当有数据需要提交时    
        if (null != outputStr) {  
            OutputStream outputStream = httpUrlConn.getOutputStream();    
            // 注意编码格式，防止中文乱码    
            outputStream.write(outputStr.getBytes("UTF-8"));    
            outputStream.close();    
        }    
          
        // 将返回的输入流转换成字符串    
        InputStream inputStream = httpUrlConn.getInputStream();    
        InputStreamReader inputStreamReader = new InputStreamReader(inputStream, "utf-8");    
        BufferedReader bufferedReader = new BufferedReader(inputStreamReader);    
          
        String str = null;    
        while ((str = bufferedReader.readLine()) != null) {    
            buffer.append(str);    
        }    
        bufferedReader.close();    
        inputStreamReader.close();    
        // 释放资源    
        inputStream.close();    
        inputStream = null;    
        httpUrlConn.disconnect();   
        String result = buffer.toString();  
        System.out.println("请求结果==>" + result);  
        return result;    
    }    
      
    /**  
     * 证书信任管理器（用于https请求）  
     *   
     * @author liufeng  
     * @date 2013-08-08  
     */    
    public static class MyX509TrustManager implements X509TrustManager {    
        
        public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {    
        }    
        
        public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {    
        }    
        
        public X509Certificate[] getAcceptedIssuers() {    
            return null;    
        }    
    }  
}
```

### 2. JS跨域请求
在网上看了好多方法:

* 1）iframe
* 2）document.domain
* 3）window.name
* 4）script
* 5）XDomainRequest (IE8+)
* 6）XMLHTTPRequest (Firefox3.5+)
* 7）postMessage (HTML5)
* 8）后台代理

**本文此处讲解一下JsonP的处理注意事项**

JSONP的实现思路很简单

* 1）前端创建script标记，设置src，添加到head中（你可以往body中添加）；
* 2）后台返回一个js变量jsonp，这个jsonp就是请求后的JSON数据；
* 3）回调完成后删除script标记（还有一些清理工作如避免部分浏览器内存泄露等）。

客户端直接使用JsonP请求：

```javascript
$.ajax({    
        url:"http://crossdomain.com/services.php",    
        dataType:'jsonp',    
        data:'',    
        jsonp:'callback',    
        success:function(result) {    
            for(var i in result) {    
                alert(i+":"+result[i]);//循环输出a:1,b:2,etc.    
            }    
        },    
        timeout:3000    
    }); 
```

服务器端处理：(Play Framework写的服务器)

```Java
String callback = request.params.get("callback");  
String result = callback + "(" + JSON.toJSONString(dataMap) + ")";  
renderText(result); 
```

返回时数据用callback+()包上。

### 3. 利用客户端解决方案改进跨域通信
<http://www.ibm.com/developerworks/cn/web/wa-crossdomaincomm/>

