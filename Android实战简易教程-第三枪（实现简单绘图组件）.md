首先我们要了解触摸事件（OnTouchListener）指的是当用户接触到屏幕之后所产生的一种事件形式，而当用户在屏幕上划过时，可以使用触摸事件取得用户当前的坐标。
## 一、坐标显示

在实现画图功能之前，我们先利用触摸事件获得当前触摸的坐标。
```xml
main.xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" ></TextView>

</LinearLayout>

```
代码非常简单，只引入一个TextView控件，下面看一下MainActivity代码：
```java
package org.yayun.demo;

import android.app.Activity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnTouchListener;
import android.widget.TextView;

public class MainActivity extends Activity {
	private TextView textView;
	
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
	textView=(TextView)findViewById(R.id.text);
	textView.setOnTouchListener(new OnTouchListener() {//触摸事件
		
		public boolean onTouch(View v, MotionEvent event) {
			textView.setText("X="+event.getX()+",Y="+event.getY());//获取坐标
			return false;
		}
	});

	}
}
```
运行实例：
![](http://img.blog.csdn.net/20150530151639685?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
上面可以看到实时获得当前触摸的坐标。
## 二、实现画图功能

由于OnTouch事件是在View类中定义的，所以如果想要完成绘图的操作，首先应该定义一个属于自己的组件，该组件专门进行绘图板的功能实现，而且组件类一定要继承View类，同时要覆写View类的onDraw()绘图方法。
代码如下：
```java
package org.yayun.demo;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.Point;
import android.util.AttributeSet;
import android.view.MotionEvent;
import android.view.View;

public class MyPaintView extends View{

	private List<Point> allPoints=new ArrayList<Point>();//保存所有的坐标点
	public MyPaintView(Context context, AttributeSet attrs) {
		super(context, attrs);
		super.setBackgroundColor(Color.WHITE);
		super.setOnTouchListener(new OnTouchListener() {
			
			public boolean onTouch(View v, MotionEvent event) {
				Point point=new Point((int)event.getX(),(int)event.getY());
				if(event.getAction()==MotionEvent.ACTION_DOWN){//判断按下
					allPoints=new ArrayList<Point>();//开始新的记录
					allPoints.add(point);
				}else if(event.getAction()==MotionEvent.ACTION_UP){
					allPoints.add(point);
				}else if(event.getAction()==MotionEvent.ACTION_MOVE){
					allPoints.add(point);
					MyPaintView.this.postInvalidate();//重绘
				}
				return true;//表示下面的不再执行了
			}
		});
	}
	
	@Override
	protected void onDraw(Canvas canvas) {
		Paint paint=new Paint();
		paint.setColor(Color.RED);
		if(allPoints.size()>1){
			Iterator<Point> iterator=allPoints.iterator();
			Point firstPoint=null;//开始点
			Point lastpPoint=null;//结束点
			while (iterator.hasNext()) {
				if(firstPoint==null){//找到开始点
					firstPoint=(Point)iterator.next();
				}else{
					if(lastpPoint!=null){
						firstPoint=lastpPoint;
					}
					lastpPoint=(Point)iterator.next();
					canvas.drawLine(firstPoint.x, firstPoint.y, lastpPoint.x, lastpPoint.y, paint);//画线
				}
				
			}
		}
		super.onDraw(canvas);
	}

}
```

修改main.xml，将新建布局引入：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <org.yayun.demo.MyPaintView
        android:id="@+id/paintView"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
    </org.yayun.demo.MyPaintView>

</LinearLayout>

```
MainActivity不用加入任何东西：
```java
package org.yayun.demo;

import android.app.Activity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnTouchListener;
import android.widget.TextView;

public class MainActivity extends Activity {

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器

	}
}

```
运行实例：
![](http://img.blog.csdn.net/20150530160208010?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 总结

1.触摸事件OnTouchListener及onTouch()方法；
2.event.getX()//利用MotionEvent获取坐标的方法getX()
3.onDraw()方法和如何使用Canvas进行绘图的操作，而本次绘制是一条线（canvas.drawLine()）。
 
喜欢的朋友可以关注我！

 