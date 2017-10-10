---
title: ScrollView内嵌ViewPager导致ViewPager滑动困难问题
date: 2014-08-08 20:08
tags:
  - Android
---
解决方式：重写ScrollView，然后在xml中定义布局的时候，使用自定义的PagerScrollView而不是系统的ScrollView即可。

重点在于重写父类的`onInterceptTouchEvent`方法

```Java
import android.content.Context;  
import android.util.AttributeSet;  
import android.view.GestureDetector;  
import android.view.GestureDetector.SimpleOnGestureListener;  
import android.view.MotionEvent;  
import android.widget.ScrollView;  
  
public class PagerScrollView extends ScrollView {  
  
    private GestureDetector mGestureDetector;  
  
    public PagerScrollView(Context context, AttributeSet attrs, int defStyle) {  
        super(context, attrs, defStyle);  
        init();  
    }  
  
    public PagerScrollView(Context context) {  
        super(context);  
        init();  
    }  
  
    public PagerScrollView(Context context, AttributeSet attrs) {  
        super(context, attrs);  
        init();  
    }  
  
    private void init() {  
        mGestureDetector = new GestureDetector(getContext(),  
                new YScrollDetector());  
        setFadingEdgeLength(0);  
    }  
  
    @Override  
    public boolean onInterceptTouchEvent(MotionEvent ev) {  
        return super.onInterceptTouchEvent(ev)  
                && mGestureDetector.onTouchEvent(ev);  
    }  
  
    private class YScrollDetector extends SimpleOnGestureListener {  
        @Override  
        public boolean onScroll(MotionEvent e1, MotionEvent e2,  
                float distanceX, float distanceY) {  
              
            if (Math.abs(distanceY) >= Math.abs(distanceX)) {  
                return true;  
            }  
            return false;  
        }  
    }  
}
```
