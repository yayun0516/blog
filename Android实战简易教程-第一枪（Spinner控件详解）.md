本教程简单实用，大家喜欢的话可以关注我，谢谢！
下拉列表框是一种常见的图形组件，与其他选择组件相比，可以有效的节省屏幕空间，在Android中可以使用android.widget.Spinner类来实现。
下拉列表框中的列表项有以下两种配置方式。
**方式一**、通过资源文件配置，例如定义一个values\city_data.xml的文件，在定义数据内容时需要使用<string-array>元素指定，定义内容如下：
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<string-array name="city_labels">
	    <item>北京 </item>
	    <item>上海 </item>
	    <item>广州 </item>
	    <item>深圳 </item>
	</string-array>
</resources>
```

**方式二**、通过android.widget.ArrayAdapter类读取资源文件或者指定具体设置的数据。
## 方式一

1.定义main.xml文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="请选择您喜欢的城市：" />

    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:entries="@array/city_labels" ><!-- 载入数据 -->
    </Spinner>

</LinearLayout>
```

其实这是你就可以运行模拟器了：
![](http://img.blog.csdn.net/20150530110402070?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
这是可以看到数据已经加入到Spinner里面去了，我们发现这时的控件只是徒有其表，没有什么作用，要想实现监听怎么做呢？
我们改动一下main.xml文件：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="请选择您喜欢的城市：" />

    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:entries="@array/city_labels" > <!-- 载入数据 -->
    </Spinner>

    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />

</LinearLayout>
```
然后编辑MainActivity文件：
```java
package org.yayun.demo;

import org.yayun.demo.R;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.TextView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.Spinner;

public class MainActivity extends Activity {
	private Spinner spinner;
	private TextView textView;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
		spinner = (Spinner) findViewById(R.id.spinner);
		textView = (TextView) findViewById(R.id.text);
		spinner.setOnItemSelectedListener(new OnItemSelectedListener() {

			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				String[] cities = getResources().getStringArray(
						R.array.city_labels);//获取列表数据
				textView.setText("您喜欢的城市是：" + cities[position]);//显示

			}

			public void onNothingSelected(AdapterView<?> parent) {
				// TODO Auto-generated method stub

			}
		});

	}
}
```
运行如下：
![](http://img.blog.csdn.net/20150530111454551?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 方式二

修改main.xml代码：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="请选择您喜欢的城市：" />

    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:entries="@array/city_labels" > <!-- 载入数据 -->
    </Spinner>

    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <Spinner
        android:id="@+id/spinnerCountry"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" > <!-- 动态载入数据 -->
    </Spinner>

</LinearLayout>
```
修改MainActivity程序：
```java
package org.yayun.demo;

import java.util.ArrayList;
import java.util.List;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemSelectedListener;
import android.widget.ArrayAdapter;
import android.widget.Spinner;
import android.widget.TextView;
import android.widget.Toast;

public class MainActivity extends Activity {
	private Spinner spinner, spinnerCountry;
	private TextView textView;
	private List<CharSequence> data = null;
	private ArrayAdapter<CharSequence> adapter;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
		spinner = (Spinner) findViewById(R.id.spinner);
		textView = (TextView) findViewById(R.id.text);
		spinnerCountry = (Spinner) findViewById(R.id.spinnerCountry);

		spinnerCountry.setPrompt("选择国籍：");// 在列表中显示
		data = new ArrayList<CharSequence>();
		data.add("中國");
		data.add("美國");
		data.add("日本");
		adapter = new ArrayAdapter<CharSequence>(this,
				android.R.layout.simple_spinner_dropdown_item, this.data);//定义下拉列表
		spinnerCountry.setAdapter(adapter);
		spinner.setOnItemSelectedListener(new OnItemSelectedListener() {

			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				String[] cities = getResources().getStringArray(
						R.array.city_labels);// 获取列表数据
				textView.setText("您喜欢的城市是：" + cities[position]);// 显示

			}

			public void onNothingSelected(AdapterView<?> parent) {
				// TODO Auto-generated method stub

			}
		});
		spinnerCountry.setOnItemSelectedListener(new OnItemSelectedListener() {

			public void onItemSelected(AdapterView<?> parent, View view,
					int position, long id) {
				String[] countries = data.toArray(new String[data.size()]);// 获取列表数据
				Toast.makeText(MainActivity.this, "您的国籍是："+countries[position], Toast.LENGTH_SHORT).show();
				
				
			}

			public void onNothingSelected(AdapterView<?> parent) {
				// TODO Auto-generated method stub
				
			}
		});

	}
}

```
运行实例如下：
![](http://img.blog.csdn.net/20150530115845689?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 总结：

1.定义数据内容时需要使用<string-array>元素指定；
2.android:entries="@array/city_labels"载入文本资源；
3.String[] cities = getResources().getStringArray(R.array.city_labels);//获取资源数据的方法
4.String 和 CharSequence 关系
String 继承于CharSequence，也就是说String也是CharSequence类型。
CharSequence是一个接口，它只包括length(), charAt(int index), subSequence(int start, int end)这几个API接口。除了String实现了CharSequence之外，StringBuffer和StringBuilder也实现了CharSequence接口。
需要说明的是，CharSequence就是字符序列，String, StringBuilder和StringBuffer本质上都是通过字符数组实现的！
5.提示信息的设置：spinnerCountry.setPrompt("选择国籍：");// 在列表中显示
6.此外可以用adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);//来设置显示风格
 
 