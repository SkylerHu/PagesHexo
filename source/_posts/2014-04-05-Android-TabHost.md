---
title: Android TabHost的三中实现方式
date: 2014-04-05 19:19
tags:
  - Android
---
* Android
* TabHost

##### 1. 继承TabActivity实现，此方法最为简单，但灵活性、实用性不高
此时的setup可以省略不写

##### 2. 继承Activity自定义TabHost，自己写布局（此方法最为常用）
	
layout.xml

	<TabHost >
		<span style="white-space:pre">	</span><TabWidget ></TabWidget>
		<span style="white-space:pre">	</span><FrameLayout >
		<span style="white-space:pre">		</span><LinearLayout ></LinearLayout>
		<span style="white-space:pre">		</span><LinearLayout ></LinearLayout>
			...
		<span style="white-space:pre">	</span></FrameLayout>
	</TabHost>

Activity.class

	TabHost tabHost = (TabHost)findViewByID();  
	tabHost.setup();  	//此时的setup不可省略不写


##### 3. 继承ActivityGroup，结合RadioButton(和FrameLayout)实现TabHost
需有mTabHost.setup(getLocalActivityManager());
