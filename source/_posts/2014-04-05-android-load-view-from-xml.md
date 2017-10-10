---
title: Android动态加载View的几种方法
date: 2014-04-05 18:57
tags:
  - Android
---
* Android
* 加载View

有以下加载方式

1. View view = LayoutInflater.from(context).inflate(要装载的界面的id, 根节点);//context为this,根节点一般为null
view.findViewByID();//与inflate配套使用的。
setContentView();//配套使用findViewByID(动态界面中的控件的id);

2. LayoutInflater inflater = ((Activity)context).getLayoutInflater();


3. 另一种方法获得
LayoutInflater inflater = (LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVER);
view = inflater.inflater (resource, root);

4. view = View.inflate(context, R.layout.header, null);
