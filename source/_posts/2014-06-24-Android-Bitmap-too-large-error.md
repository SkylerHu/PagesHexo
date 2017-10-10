---
title: Bitmap too large to be uploaded into a texture,Hardware accelerated
date: 2014-06-24 11:47
tags:
  - Android
---
>Android的硬件加速及可能导致的问题

#### 问题
在Android 4.0的某些设备上，在View刷新时会出现花屏和屏幕上的某些View错位的现象。 经过调查后发现`adb logcat`中出现很多`OpenGLRenderer: 0x501`的错位：

```
09-18 14:34:39.090: DEBUG/OpenGLRenderer(3104): GL error from OpenGLRenderer: 0x501
09-18 14:34:39.386: DEBUG/OpenGLRenderer(3104): GL error from OpenGLRenderer: 0x501
09-18 14:34:39.656: DEBUG/OpenGLRenderer(3104): GL error from OpenGLRenderer: 0x501
```

从这个日志，初步怀疑是硬件加速导致的问题。 经过分析发现使用了比较复杂的自定义View，可能会导致硬件加速渲染出错。


### 硬件加速的优点与缺点

硬件加速能使用GPU来加速2D图像的渲染速度，但是硬件加速并不能完全支持所有的渲染操作， 针对自定义的View，硬件加速可能导致渲染出现错误。 如果有自定义的View，需要在硬件加速的设备上进行测试，如果出现渲染的问题，需要关闭硬件加速。

### 开启和关闭硬件加速
对硬件加速的开关可以在不同的级别进行控制：

* Application
* Activity
* Windows
* View

#### 1. Application级别
在Applciation级别控制硬件加速的开关

```
<application android:hardwareAccelerated="true" ...>
```
#### 2. Activity级别
可以对单个的Activity控制是否启用硬件加速：

```
<application android:hardwareAccelerated="true">
    <activity ... />
    <activity android:hardwareAccelerated="false" />
</application>
```

#### 3. Window级别
在指定的View上关闭硬件加速：

```
myView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
```

或者使用`android:layerType="software"`来关闭硬件加速：

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical"
    android:paddingLeft="2dp"
    android:layerType="software"
    android:paddingRight="2dp" />
```

### 如何判断一个View是否启用了硬件加速

`View.isHardwareAccelerated()` returns true if the View is attached to a hardware accelerated window.

`Canvas.isHardwareAccelerated()` returns true if the Canvas is hardware accelerated

**参考：**<http://developer.android.com/guide/topics/graphics/hardware-accel.html>
