Android用的最多的，也最难用的应该就是ListView了，下面我们研究一下它的用法。
## 一、最简单的ListView

我们简单介绍一下ListView的最简单用法，不使用布局文件：
```java
package org.lxh.demo;

import android.app.Activity;
import android.os.Bundle;
import android.widget.ArrayAdapter;
import android.widget.ListView;

public class MainActivity extends Activity {
	private String[] arrays = { "Sunday", "Monday", "Tuesday", "Wednesday",
			"Thursday", "Friday", "Saturday" };
	private ListView listView;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		listView = new ListView(this);
		listView.setAdapter(new ArrayAdapter<String>(this,
				android.R.layout.simple_expandable_list_item_1, arrays));//利用ArrayAdapter对数据进行包装
		super.setContentView(listView);//将ListView设置为显示界面
	}

}
```
运行实例如下：
![## ](http://img.blog.csdn.net/20150530205841863?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 二、结合ArrayAdapter实现较复杂的ListView

1.main.xml代码：
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
	<ListView
        android:id="@+id/list_view"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" /></LinearLayout>
```

2.fruit_item.xml代码：
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <ImageView
        android:id="@+id/fruit_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <TextView
        android:id="@+id/fruit_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginLeft="10sp" />
</LinearLayout>
```
3.Fruit.java代码如下：
```java
package org.lxh.demo;

public class Fruit {
	private String name;
	private int imageId;

	public Fruit(String name, int imageId) {
		this.name = name;
		this.imageId = imageId;
	}

	public String getName() {
		return name;
	}

	public int getImageId() {
		return imageId;
	}
}
```

4.集成ArrayAdapter的FruitAdapter.java代码如下：
```java
package org.lxh.demo;

import java.util.List;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.ImageView;
import android.widget.TextView;

public class FruitAdapter extends ArrayAdapter<Fruit> {
	private int resourceId;

	public FruitAdapter(Context context, int textViewResourceId,
			List<Fruit> objects) {
		super(context, textViewResourceId, objects);
		resourceId = textViewResourceId;
	}

	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		Fruit fruit = getItem(position);
		View view;
		ViewHolder viewHolder;
		if (convertView == null) {
			view = LayoutInflater.from(getContext()).inflate(resourceId, null);
			viewHolder = new ViewHolder();
			viewHolder.fruitImage = (ImageView) view
					.findViewById(R.id.fruit_image);
			viewHolder.fruitName = (TextView) view
					.findViewById(R.id.fruit_name);
			view.setTag(viewHolder);
		} else {
			view = convertView;
			viewHolder = (ViewHolder) view.getTag();
		}

		viewHolder.fruitImage.setImageResource(fruit.getImageId());
		viewHolder.fruitName.setText(fruit.getName());
		return view;

	}

	class ViewHolder {
		ImageView fruitImage;
		TextView fruitName;
	}

}

```
5.MainActivity.java代码如下：
```java
package org.lxh.demo;

import java.util.ArrayList;
import java.util.List;

import android.app.Activity;
import android.os.Bundle;
import android.widget.ListView;

public class Hello extends Activity {
	private List<Fruit> fruitList = new ArrayList<Fruit>();

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
		initFruits();
		FruitAdapter adapter = new FruitAdapter(Hello.this,
				R.layout.fruit_item, fruitList);
		ListView listView = (ListView) findViewById(R.id.list_view);
		listView.setAdapter(adapter);

	}

	private void initFruits() {
		Fruit appleFruit = new Fruit("Apple", R.drawable.apple_pic);
		fruitList.add(appleFruit);
		Fruit bananaFruit = new Fruit("Banana", R.drawable.banana_pic);
		fruitList.add(bananaFruit);
		Fruit orangeFruit = new Fruit("Orange", R.drawable.orange_pic);
		fruitList.add(orangeFruit);
		Fruit waterFruit = new Fruit("Apple", R.drawable.watermelon_pic);
		fruitList.add(waterFruit);

	}
}

```
运行实例如下：
![](http://img.blog.csdn.net/20150530210626850?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 ## 三、结合上下文菜单实现单项的删除
修改MainActivity.java程序如下：
```java

package org.lxh.demo;

import java.util.ArrayList;
import java.util.List;

import android.app.Activity;
import android.os.Bundle;
import android.view.ContextMenu;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.ContextMenu.ContextMenuInfo;
import android.widget.ListView;
import android.widget.AdapterView.AdapterContextMenuInfo;

public class Hello extends Activity {
	private List<Fruit> fruitList = new ArrayList<Fruit>();
	FruitAdapter adapter;

	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState); // 生命周期方法
		super.setContentView(R.layout.main); // 设置要使用的布局管理器
		initFruits();
		adapter = new FruitAdapter(Hello.this, R.layout.fruit_item, fruitList);
		ListView listView = (ListView) findViewById(R.id.list_view);
		listView.setAdapter(adapter);
		super.registerForContextMenu(listView);//注册上下文菜单

	}

	@Override
	public void onCreateContextMenu(ContextMenu menu, View v,// 创建菜单项
			ContextMenuInfo menuInfo) {

		super.onCreateContextMenu(menu, v, menuInfo);
		menu.setHeaderTitle("选择操作");
		menu.add(Menu.NONE, Menu.FIRST + 1, 1, "删除");
		menu.add(Menu.NONE, Menu.FIRST + 2, 2, "取消");
	}

	@Override
	public boolean onContextItemSelected(MenuItem item) {// 选择监听
		AdapterContextMenuInfo acmiRef = (AdapterContextMenuInfo) item
				.getMenuInfo();// 用来获取item信息哎，重要
		int removeIndex = acmiRef.position;
		switch (item.getItemId()) {
		case Menu.FIRST + 1:
			fruitList.remove(removeIndex);
			adapter.notifyDataSetChanged();// 删除后刷新ListView

			break;
		case Menu.FIRST + 2:

			break;

		default:
			break;
		}
		return false;
	}

	private void initFruits() {
		Fruit appleFruit = new Fruit("Apple", R.drawable.apple_pic);
		fruitList.add(appleFruit);
		Fruit bananaFruit = new Fruit("Banana", R.drawable.banana_pic);
		fruitList.add(bananaFruit);
		Fruit orangeFruit = new Fruit("Orange", R.drawable.orange_pic);
		fruitList.add(orangeFruit);
		Fruit waterFruit = new Fruit("Apple", R.drawable.watermelon_pic);
		fruitList.add(waterFruit);

	}
}

```
运行实例：
![](http://img.blog.csdn.net/20150530224518663?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
选择删除后：
![](http://img.blog.csdn.net/20150530224609393?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWF5dW4wNTE2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
## 总结

1.super.registerForContextMenu(listView);//注册上下文菜单
2.adapter.notifyDataSetChanged();// 删除后刷新ListView
3.AdapterContextMenuInfo acmiRef = (AdapterContextMenuInfo) item.getMenuInfo();// 用来获取当前item信息，重要
   int removeIndex = acmiRef.position;//获取点击位置的坐标