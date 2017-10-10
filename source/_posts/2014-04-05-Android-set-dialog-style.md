---
title: Android自定义Dialog 软键盘、大小的设置
date: 2014-04-05 19:03
tags:
 - Android
---
### Dialog中EditText可弹出软键盘

```xml
<style name="CustomDialogStyle" parent="@android:style/Theme.Dialog">  
  <item name="android:windowFrame">@null</item>  
  <item name="android:windowIsFloating">true</item>  
  <item name="android:windowIsTranslucent">true</item>  
  <item name="android:windowNoTitle">true</item>  
  <item name="android:background">@android:color/transparent</item>  
  <item name="android:windowBackground">@android:color/transparent</item>  
  <item name="android:backgroundDimEnabled">true</item>  
  <item name="android:backgroundDimAmount">0.6</item>  
</style>
```

把style设置到dialog中

```Java
Dialog ad = new Dialog(context,R.style.CustomDialogStyle);  
ad.show();  
Window window = ad.getWindow();  
window.setBackgroundDrawable(new ColorDrawable(0));    
window.setContentView(R.layout.cancel_sos_dialog);
```

### Dialog中设置大小

代码承接上面的代码

```Java
WindowManager.LayoutParams  lp= window.getAttributes();    
lp.width=screenWidth - 50;//定义宽度    
lp.height=LayoutParams.WRAP_CONTENT;//定义高度    
window.setAttributes(lp);  
```
