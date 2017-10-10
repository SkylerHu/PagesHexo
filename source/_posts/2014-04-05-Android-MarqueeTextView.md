---
title: Android文字跑马灯
date: 2014-04-05 20:02
tags:
  - Android
---
### 1. 继承TextView自定义控件

```Java
package com.anjoyo.musicplayer.define;  
import android.content.Context;  
import android.graphics.Rect;  
import android.util.AttributeSet;  
import android.widget.TextView;  
  
public class MarqueeTextView extends TextView {  
  
    public MarqueeTextView(Context context) {  
        super(context);  
//      android:ellipsize="marquee"   
//      android:focusable="true"   
//      android:marqueeRepeatLimit="marquee_forever"   
//      android:focusableInTouchMode="true"   
//      android:scrollHorizontally="true"  
////      android:singleLine="true"  
//      setSingleLine(true);  
//      setEllipsize(TruncateAt.MARQUEE);  
//      setFocusable(true);  
//      setMarqueeRepeatLimit(Integer.MAX_VALUE);  
//      setFocusableInTouchMode(true);  
    }  
      
    public MarqueeTextView(Context context, AttributeSet attrs, int defStyle) {  
        super(context, attrs, defStyle);  
        // TODO Auto-generated constructor stub  
    }  
  
    public MarqueeTextView(Context context, AttributeSet attrs) {  
        super(context, attrs);  
        // TODO Auto-generated constructor stub  
    }  
  
    @Override  
    protected void onFocusChanged(boolean focused, int direction,  
            Rect previouslyFocusedRect) {  
        if (focused) {  
            super.onFocusChanged(focused, direction, previouslyFocusedRect);  
        }  
    }  
  
    @Override  
    public void onWindowFocusChanged(boolean hasWindowFocus) {  
        if (hasWindowFocus) {  
            super.onWindowFocusChanged(hasWindowFocus);  
        }  
    }  
      
    @Override  
    public boolean isFocused() {  
        return true;  
    }  
      
}  
```

### 2. 在布局中的使用

```xml
<com.anjoyo.musicplayer.define.MarqueeTextView  
    android:id="@+id/tv_folder_filepath"  
    android:layout_width="wrap_content"  
    android:layout_height="20dp"  
    android:gravity="clip_vertical"  
        style="@style/MarqueeTextView"  
        android:textColor="#ffffff"  
        android:textSize="12sp"   
    android:layout_below="@+id/tv_folder_name"  
 /> 
```

### 3. style.xml

```xml
<style name="MarqueeTextView">  
<span style="white-space:pre">  </span><item name="android:ellipsize">marquee</item>  
        <item name="android:focusable">true</item>  
        <item name="android:marqueeRepeatLimit">marquee_forever</item>  
        <item name="android:focusableInTouchMode">true</item>  
        <item name="android:scrollHorizontally">true</item>  
        <item name="android:singleLine">true</item>  
</style>  
```
