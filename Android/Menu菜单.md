---
title: Menu菜单
date: 2018-12-28 15:22:00
categories: Android
---
&emsp;&emsp;`Android`提供了标准的`XML`格式的资源文件来定义菜单项，并且对所有菜单类型都支持，推荐使用`XML`资源文件来定义菜单。之后再把它`Inflater`到`Activity`或者`Fragment`中，而不是在`Activity`中使用代码声明。
&emsp;&emsp;菜单的`XML`资源文件需要创建在`/res/menu/`目录下，并且包含一下几个元素：

- `<menu>`：定义一个`Menu`，它是一个菜单资源文件的根节点，里面可以包含一个或者多个`<item>`和`<group>`元素。
- `<item>`：创建一个`MenuItem`，代表了菜单中一个选项。
- `<group>`：对菜单项进行分组，可以以组的形式操作菜单项。

&emsp;&emsp;`<group>`是对菜单进行分组，分组后的菜单显示效果并没有区别，唯一的区别在于可以针对菜单组进行操作，这样对于分类的菜单项操作起来更方便：

- `Menu.setGroupCheckable`：菜单组内的菜单是否都可选。
- `Menu.setGroupVisible`：是否隐藏菜单组的所有菜单。
- `Menu.setGroupEnabled`：菜单组的菜单是否有用。

&emsp;&emsp;当创建好一个`XML`菜单资源文件之后，可以使用`MenuInflater.inflate`方法填充菜单资源，使`XML`资源变成一个可编程的对象。

### Options menu

&emsp;&emsp;`OptionMenu`(选项菜单)是在单击手机上的菜单键(`MENU`)时出现，必须设备具有菜单按钮才可以触发。因为屏幕的限制，最多只能展示`6`个菜单项，如果定义的菜单项超出了`6`个，其他的菜单项将被隐藏，第`6`个菜单将会显示`更多`，点击展开更多的菜单。虽说在`Android 3.0`之后不再推荐使用选项菜单，但是如果使用了在`Android 3.0`之后的设备上，选项菜单项将被默认转移到`ActionBar`中，这个可以通过`android:showAsAction`属性控制。
&emsp;&emsp;创建选项菜单的核心步骤如下：

- 重写`Activity`的`onCreateOptionMenu(Menu menu)`方法，调用`MenuInflater`的`inflate`方法添加菜单项(`MenuItem`)，当菜单第一次被加载时调用。
- 重写`Activity`的`OptionsItemSelected(MenuItem item)`来响应菜单项(`MenuItem`)的点击事件。

&emsp;&emsp;在`res/menu/menu.xml`文件中定义菜单项：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/startGame"
        android:title="开始游戏" />
    <item
        android:id="@+id/endGame"
        android:title="结束游戏" />
</menu>
```

`Java`代码如下：

``` java
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.app.Activity;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
​
    @Override
    /* 重写onCreateOptionMenu(Menu menu)方法，当菜单第一次被加载时调用 */
    public boolean onCreateOptionsMenu(Menu menu) {
        MenuInflater menuInflater = new MenuInflater(this);
        /* 填充选项菜单(读取XML文件，解析并加载到Menu组件上) */
        menuInflater.inflate(R.menu.menu, menu);
        return super.onCreateOptionsMenu(menu);
    }
​
    @Override
    /* 重写OptionsItemSelected(MenuItem item)来响应菜单项(MenuItem)的点击事件(根据id来区分是哪个item) */
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.startGame:
                Toast.makeText(MainActivity.this, "开始游戏", Toast.LENGTH_SHORT).show();
                break;
            case R.id.endGame:
                Toast.makeText(MainActivity.this, "结束游戏", Toast.LENGTH_SHORT).show();
                break;
            default:
                break;
        }
​
        return super.onOptionsItemSelected(item);
    }
}
```

### Context menu

&emsp;&emsp;`Context menu`是上下文菜单，顾名思义是与上下文(环境)有关。当长时间按住某个组件不放，就会弹出`Context menu`。创建上下文菜单的核心步骤如下：

- 覆盖`Activity的onCreateContextMenu(Menu menu)`方法，调用`MenuInflater`的`inflate`方法添加菜单项(`MenuItem`)。
- 覆盖`Activity的onContextItemSelected(MenuItem iitem)`来响应事件。
- 调用`registerForContextMenu`方法来为视图注册上下文菜单。

&emsp;&emsp;`res/menu/menu.xml`文件内容和上文相同，`Java`文件如下：

``` java
import android.os.Bundle;
import android.view.ContextMenu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.app.Activity;
import android.view.View;
import android.widget.ImageView;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ImageView imageView = (ImageView) findViewById(R.id.imageView1);
        /* 为图片绑定上下文菜单 */
        registerForContextMenu(imageView);
    }
​
    @Override
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        MenuInflater menuInflater = new MenuInflater(this);
        /* 填充选项菜单(读取XML文件，解析并加载到Menu组件上) */
        menuInflater.inflate(R.menu.menu, menu);
        super.onCreateContextMenu(menu, v, menuInfo);
    }
​
    @Override
    /* onContextItemSelected(MenuItem item)来响应菜单项(MenuItem)的点击事件(根据id来区分是哪个item) */
    public boolean onContextItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case R.id.startGame:
                Toast.makeText(MainActivity.this, "开始游戏", Toast.LENGTH_SHORT).show();
                break;
            case R.id.endGame:
                Toast.makeText(MainActivity.this, "结束游戏", Toast.LENGTH_SHORT).show();
                break;
            default:
                break;
        }
​
        return super.onContextItemSelected(item);
    }
}
```