## 一、实现功能

废话不多说，直接上代码

strings.xml:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<string name="hello">Hello World, Hello!</string>
	<string name="app_name"></string>
	<string-array name="province">
	    <item >江苏省</item>
	    <item >山东省</item>
	    <item >浙江省</item>
	</string-array>
</resources>
```

 
看一下布局文件main.xml：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="horizontal" >

        <Spinner
            android:id="@+id/spinner1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:entries="@array/province"/>

        <Spinner
            android:id="@+id/spinner2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
    </LinearLayout>

    <TextView
        android:id="@+id/text"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" >
    </TextView>

</LinearLayout>

```
MainActivity代码如下：
```java
package org.yayun.demo;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.Dialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.View.OnFocusChangeListener;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.TextView;

public class MainActivity extends Activity {
	private Spinner spinner1;
	private Spinner spinner2;
	private TextView textView;
	private String[][] dataStrings = { { "南京市", "徐州市", "苏州市", "泰州市", "淮安市", },
			{ "济南市", "威海市", "临沂市", }, { "杭州市", "宁波市", "嘉兴市", }, };
	private ArrayAdapter<CharSequence> adapter = null;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
		spinner1 = (Spinner) findViewById(R.id.spinner1);
		spinner2 = (Spinner) findViewById(R.id.spinner2);
		textView = (TextView) findViewById(R.id.text);
		spinner1.setOnItemSelectedListener(new OnItemSelectedListener() {

			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				adapter = new ArrayAdapter<CharSequence>(MainActivity.this,
						android.R.layout.simple_spinner_item,
						dataStrings[position]);//根据position确定第二个spinner中要填充的数据
				adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);//设置显示样式
				spinner2.setAdapter(adapter);
				
			}

			public void onNothingSelected(AdapterView<?> parent) {
				// TODO Auto-generated method stub

			}
		});
		
	}
}
```

运行实例：
![](http://img.blog.csdn.net/20150530144155631?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 二、实现监听

修改MainActivity.java代码：
```java
package org.yayun.demo;

import android.app.Activity;
import android.app.AlertDialog;
import android.app.Dialog;
import android.content.DialogInterface;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.View.OnFocusChangeListener;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.ArrayAdapter;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Spinner;
import android.widget.TextView;

public class MainActivity extends Activity {
	private Spinner spinner1;
	private Spinner spinner2;
	private TextView textView;
	private String[][] dataStrings = { { "南京市", "徐州市", "苏州市", "泰州市", "淮安市", },
			{ "济南市", "威海市", "临沂市", }, { "杭州市", "宁波市", "嘉兴市", }, };
	private ArrayAdapter<CharSequence> adapter = null;
	String string1;
	String string2;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
		spinner1 = (Spinner) findViewById(R.id.spinner1);
		spinner2 = (Spinner) findViewById(R.id.spinner2);
		textView = (TextView) findViewById(R.id.text);

		spinner1.setOnItemSelectedListener(new OnItemSelectedListener() {

			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				adapter = new ArrayAdapter<CharSequence>(MainActivity.this,
						android.R.layout.simple_spinner_item,
						dataStrings[position]);// 根据position确定第二个spinner中要填充的数据
				adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);// 设置显示样式
				spinner2.setAdapter(adapter);
				string1 = parent.getItemAtPosition(position).toString();// 获取当前显示省份

			}

			public void onNothingSelected(AdapterView<?> parent) {
				// TODO Auto-generated method stub

			}
		});
		spinner2.setOnItemSelectedListener(new OnItemSelectedListener() {

			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				string2 = parent.getItemAtPosition(position).toString();// 获取当前显示省份
				textView.setText(string1 + string2);

			}

			public void onNothingSelected(AdapterView<?> parent) {
				// TODO Auto-generated method stub

			}
		});

	}
}

```
运行实例如下：
![](http://img.blog.csdn.net/20150530145214849?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
 
## 总结

1.string1 = parent.getItemAtPosition(position).toString();// 获取当前显示省份
 
喜欢的朋友可以关注我！谢谢