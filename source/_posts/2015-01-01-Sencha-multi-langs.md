---
title: Sencha实现国际化，JS动态加载js、css文件
date: 2015-01-01 08:22
tags:
  - Sencha
---
1. 新建文件messages_en.js和messages_zh_CN.js文件；
2. 文件中定义js全局变量 var Messages = {};
例：messages_zh_CN.js文件  Messages = {"title":“标题”};
message_en.js文件  Messages = {"title":“title”};
3. 在Sencha应用入口launch : function(){}中，先获取用户的语言，然后通过不同的语言去加载不同的JS文件；
```JS
/**  
     * 动态加载文件 
     * @param fileName 文件名(包含后缀名) 
     *      .js:放在js文件夹下;.css:放在css文件夹下 
     * @param 回调 
     */  
    loadJSCSSFile : function(fileName, callback){  
        var fileref = null;  
        var arr = fileName.split('.');  
        switch(arr[arr.length - 1]) {  
        case 'js':  
            fileref = document.createElement('script');  
            fileref.setAttribute('type', 'text/javascript');  
            fileref.setAttribute('src', 'js/' + fileName);  
            break;  
        case 'css':  
            fileref = document.createElement('link');  
            fileref.setAttribute('rel', 'stylesheet');  
            fileref.setAttribute('type', 'text/css');  
            fileref.setAttribute('herf', 'css/' + fileName);  
            break;  
        }  
        if(fileref != null) {  
            if (fileref.readyState) { // IE  
                fileref.onreadystatechange = function() {  
                    if (fileref.readyState == 'loaded' || fileref.readyState == 'complete') {  
                        fileref.onreadystatechange = null;  
                        callback();  
                    }  
                };  
            } else { // Others: Firefox, Safari, Chrome, and Opera  
                fileref.onload = function() {  
                    callback();  
                };  
            }  
            document.getElementsByTagName('head')[0].appendChild(fileref);  
        }  
    },  
```
4. 需要国际化的文字都在文件中以(key, value)的形式保存，用的时候，直接Messages.title
