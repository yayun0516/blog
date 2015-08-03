ImageSwitcher 组件的主要功能是完成图片的切换显示，例如用户在进行图片浏览时，可以通过单击按钮逐张切换显示的图片，在进行切换时还可以加入一些动画效果。
如果想进行实现图片的切换功能，则定义的Activity类还必须实现ViewSwitcher.ViewFactory接口，以指定切换视图的操作工厂，此接口定义如下：
**android.widget**
接口 **ViewSwitcher.ViewFactory**

包容类：
**ViewSwitcher**

**public static interface ViewSwitcher.ViewFactory
Creates views in a ViewSwitcher.**
 
方法摘要
 **View	makeView() **
          Creates a new View to be added in a ViewSwitcher.
 
方法详细信息
**makeView**

**View makeView()**
Creates a new View to be added in aViewSwitcher.
返回：
**a View**
本接口中只存在一个makeView()方法，此方法的主要功能是返回一个View对象的多干设置参数。
下面看一下代码：
1.main.xml代码：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/MyLayout"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <ImageSwitcher
        android:id="@+id/imageSwitcher"
        android:layout_gravity="center"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" ></ImageSwitcher>

    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >

        <Button
            android:id="@+id/btnPrevious"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:enabled="false"
            android:text="上一张" ></Button>

        <Button
            android:id="@+id/btnNext"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:enabled="true"
            android:text="下一张" ></Button>
    </LinearLayout>

</LinearLayout>

```
2.MainActivity.java代码如下：
```java
package org.yayun.demo;

import android.R.anim;
import android.R.integer;
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.ViewGroup.LayoutParams;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.ImageSwitcher;
import android.widget.ImageView;
import android.widget.ViewSwitcher.ViewFactory;

public class MainActivity extends Activity {
	private ImageSwitcher imageSwitcher;
	private Button btnPrevious;
	private Button btnNext;
	private int foot=0;
	private int[] imgRes=new int[]{R.drawable.ispic_a,R.drawable.ispic_b,R.drawable.ispic_c,R.drawable.ispic_d,R.drawable.ispic_e,};
	
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
	imageSwitcher=(ImageSwitcher)findViewById(R.id.imageSwitcher);
	btnPrevious=(Button)findViewById(R.id.btnPrevious);
	btnNext=(Button)findViewById(R.id.btnNext);
	
	imageSwitcher.setFactory(new ViewFactory() {//设置转化工厂
		
		public View makeView() {//覆写方法
			ImageView imageView=new ImageView(MainActivity.this);
			imageView.setBackgroundColor(0xFFFFFFFF);
			imageView.setScaleType(ImageView.ScaleType.CENTER);//居中显示
			imageView.setLayoutParams(new ImageSwitcher.LayoutParams(LayoutParams.FILL_PARENT,LayoutParams.FILL_PARENT));//定义组件
			return imageView;
		}
	});
	imageSwitcher.setImageResource(imgRes[foot++]);//初始化时显示，必须放在工厂后面，否则会报NullPointerException
	imageSwitcher.setInAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_in));//设置动画
	imageSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_out));//设置动画
	btnPrevious.setOnClickListener(new OnClickListener() {
		
		public void onClick(View v) {
			MainActivity.this.imageSwitcher.setImageResource(imgRes[foot--]);
			MainActivity.this.checkBtnEnable();
			
		}
	});
	btnNext.setOnClickListener(new OnClickListener() {
		
		public void onClick(View v) {
			MainActivity.this.imageSwitcher.setImageResource(imgRes[foot++]);
			MainActivity.this.checkBtnEnable();
			
		}
	});

	}

	protected void checkBtnEnable() {//判断按钮可用状态
		if(this.foot<this.imgRes.length-1){
			this.btnNext.setEnabled(true);
		}else{
			this.btnNext.setEnabled(false);
		}
		if(this.foot==0){
			this.btnPrevious.setEnabled(false);
		}else {
			this.btnPrevious.setEnabled(true);
		}
		
	}
}

```
3.运行实例如下：
![](http://img.blog.csdn.net/20150604102716248?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 总结

1.不设置setFactory()方法会出现NullPointerException错误。
2.设置动画的方法：setInAnimation(AnimationUtils.loadAnimation(this, android.R.anim.fade_in))；
3.自定义判断按钮是否可用的方法checkBtnEnable()