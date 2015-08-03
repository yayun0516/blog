我们知道，我们编写的应用程序都是有一定内存限制的，程序占用了过高的内存就容易出现OOM(OutOfMemory)异常。因此在展示高分辨率图片的时候，最好先将图片进行压缩，压缩后的图片大小应该和用来展示它的控件大小相近，这样可以兼顾显示效果和内存占用。
BitmapFactory.Options这个类，有一个字段叫做 inJustDecodeBounds 。SDK中对这个成员的说明是这样的：
If set to true, the decoder will return null (no bitmap), but the out…
也就是说，如果我们把它设为true，那么BitmapFactory.decodeFile(String path, Options opt)并不会真的返回一个Bitmap给你，它仅仅会把它的宽，高取回来给你，这样就不会占用太多的内存，也就不会那么频繁的发生OOM了。
下面我们通过具体实例来展示怎么实现缩略图。
1.布局文件：
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <ImageView
        android:id="@+id/imageView1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:src="@drawable/mei" ></ImageView>

    <ImageView
        android:id="@+id/imageView2"
        android:layout_width="wrap_content"
        android:layout_below="@+id/imageView1"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:src="@drawable/mei" ></ImageView>

</RelativeLayout>
```

2.MainActivity.java代码如下：
```java
package org.yayun.demo;

import java.io.InputStream;

import android.app.Activity;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Matrix;
import android.os.Bundle;
import android.widget.ImageView;

public class MainActivity extends Activity {
	private ImageView imageView1;
	private ImageView imageView2;
	Bitmap mBitmap;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		initView();
	
	}

	private void initView(){
		imageView1=(ImageView)findViewById(R.id.imageView1);
		imageView2=(ImageView)findViewById(R.id.imageView2);
		//读取资源图片
		mBitmap=readBitMap();
		//对资源图片进行缩放
		imageView2.setImageBitmap(zoomBitmap(mBitmap, mBitmap.getWidth()/4, mBitmap.getHeight()/4));
	}
	
	
	/**
	 * 读取资源图片
	 * @return 
	 */
	private Bitmap readBitMap(){
		BitmapFactory.Options opt=new BitmapFactory.Options();
		/*
		 * 设置让解码器以最佳方式解码
		 */
		opt.inPreferredConfig=Bitmap.Config.RGB_565;
		//下面两个字段需要组合使用
		opt.inPurgeable=true;
		opt.inInputShareable=true;
		/*
		 * 获取资源图片
		 */
		InputStream is=this.getResources().openRawResource(R.drawable.mei);
		return BitmapFactory.decodeStream(is, null, opt);
	}

	
	/**
	 * 缩放图片
	 * @param bitmap
	 * @param w
	 * @param h
	 * @return
	 */
	public  Bitmap zoomBitmap(Bitmap bitmap, int w, int h) {
		int width = bitmap.getWidth();
		int height = bitmap.getHeight();
		Matrix matrix = new Matrix();
		float scaleWidht = ((float) w / width);
		float scaleHeight = ((float) h / height);
		/*
		 * 通过Matrix类的postScale方法进行缩放
		 */
		matrix.postScale(scaleWidht, scaleHeight);
		Bitmap newbmp = Bitmap.createBitmap(bitmap, 0, 0, width, height, matrix, true);
		return newbmp;
	}
	
}

```
3.运行实例：
![](http://img.blog.csdn.net/20150605084450324?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
缩略图效果体现出了。
