---
title: Android的五中数据存储方式
date: 2014-04-05 19:42
tags:
  - Android
---
## 1. SharedPreference：通过键值对形式保存简单的私有数据
类似于Java中的Properties类
保存的也是key = value,是以xml文档格式保存。

```Java
//获得SharedPreferences的实例  
shared = getSharedPreferences(文件名不需要后缀,文件的创建模式);  
//文件的创建模式  
Context.MODE_APPEDN(追加)  
Context.MODE_PRIVATE(私有)  
Context.MODE_WORLD_READABLE(公有可读)  
Context.MODE_WORLD_WRITEABLE(公有可写)  
//获得可以修改的对象Editor  
editor = share.edit();  
editor.putXXX(key, value);  
//提交执行，写入文档  
editor.commit(); 
```

**查看文件**
 -> window -> preference -> showView -> file Explorer
 
 **路径：**/data/data/[your package name]/shared_prefs/
 
 ```Java
//得到写入而能当的内容  
shared.getXXX(key, 默认值);  
shared.getAll();//返回的是一个Map<String, ?>  
StringBuffer sb = new StringBuffer();  
sb.append("name = ")  
    .append(shared.getString("name", null)  
    .append("\r\n")  
    .append("password = ")  
    .append(shared.getString("password", null);  
editText.setText(sb); 
 ```
 
## 2. 文件存储：以文件的形式存储数据，内部存储——保存私有数据，外部存储——保存公有数据
### （1）内部存储
```Java
os = openFileOutput(文件名,文件创建模式同上);  
is = opFileInput(文件名)；  
//文件保存的位置：/data/data/your package mame/files/  
//获取路径        
File dir = Context.getFilesDir();  
File file = dir + “//” + FILE_NAME;  
//读取数据时应该判断文件是否存在。  
//这两种方式都是要占据项目的空间，使得系统运行时会出现空间不足的问题。 
```
### （2）外部（SD卡）存储
```Java
//先判断SDCard是否存在file explorer ./mnt/sdcard/  
if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)){  
    //获得SDCard的根目录  
	String filePath = Environment.getExternalStorageDirectory() + File.separator + "b103" + File.separator + "test.txt";  
}
```

**<span style="color: #ff0000;">配置文件中加权限</span>**

```xml
<uses-permission  android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission  android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>  
```
其他操作与普通的JAVA的IO操作没有区别。

## 3. SQLite数据库：把结构化的数据保存在一个私有的数据库中

## 4. 网络存储：把数据保存在网络上开发者自己的服务器中

## 5. XML存储：通过XML文件存储数据
也相当于文件存储的一种形式。

