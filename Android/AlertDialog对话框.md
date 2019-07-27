---
title: AlertDialog对话框
date: 2018-12-28 15:48:39
categories: Android
---
&emsp;&emsp;`AlertDialog`对话框基本使用流程如下：

- 创建`AlertDialog.Builder`对象。
- 调用`setIcon`设置图标，`setTitle`或`setCustomTitle`设置标题。
- 使用`setMessage`或其他方法设置对话框的内容。
- 调用`setButton`设置确定、取消和中立按钮。
- 调用`create`方法创建这个对象，再调用`show`方法将对话框显示出来。

### 普通对话框

&emsp;&emsp;代码如下：

``` java
import android.content.DialogInterface;
import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.app.Activity;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                AlertDialog alertDialog = new AlertDialog.Builder(MainActivity.this).create();
                alertDialog.setIcon(R.mipmap.ic_launcher);
                alertDialog.setTitle("little fool:");
                alertDialog.setMessage("To be a master!");
                alertDialog.setButton(DialogInterface.BUTTON_NEGATIVE, "否", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        Toast.makeText(MainActivity.this, "你点击了取消按钮", Toast.LENGTH_SHORT).show();
                    }
                });

                alertDialog.setButton(DialogInterface.BUTTON_POSITIVE, "是", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        Toast.makeText(MainActivity.this, "你点击了确定按钮", Toast.LENGTH_SHORT).show();
                    }
                });

                alertDialog.show();
            }
        });
    }
}
```

### 普通列表对话框

&emsp;&emsp;代码如下：

``` java
import android.content.DialogInterface;
import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.app.Activity;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                final String[] lesson = new String[]{"语文", "数学", "英语"};
                AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
                builder.setIcon(R.mipmap.ic_launcher);
                builder.setTitle("你喜欢的课程");
                builder.setItems(lesson, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        Toast.makeText(MainActivity.this, "你选择了" + lesson[which], Toast.LENGTH_SHORT).show();
                    }
                }).create();
                builder.show();
            }
        });
    }
}
```

### 单选列表对话框

&emsp;&emsp;代码如下：

``` java
import android.content.DialogInterface;
import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.app.Activity;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                final String[] lesson = new String[]{"语文", "数学", "英语"};
                AlertDialog alert = null;
                AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
                builder.setIcon(R.mipmap.ic_launcher);
                builder.setTitle("你喜欢的课程");
                alert = builder.setTitle("只能选择一个").setSingleChoiceItems(lesson, 0, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        Toast.makeText(MainActivity.this, "你选择了" + lesson[which], Toast.LENGTH_SHORT).show();
                    }
                }).create();
                alert.show();
            }
        });
    }
}
```

### 多选列表对话框

&emsp;&emsp;代码如下：

``` java
import android.content.DialogInterface;
import android.os.Bundle;
import android.support.v7.app.AlertDialog;
import android.app.Activity;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                final String[] lesson = new String[]{"语文", "数学", "英语"};
                final boolean[] checkItems = new boolean[]{false, false, false, false};
                AlertDialog alert = null;
                AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
                builder.setIcon(R.mipmap.ic_launcher);
                builder.setTitle("你喜欢的课程");
                alert = builder.setMultiChoiceItems(lesson, checkItems, new DialogInterface.OnMultiChoiceClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which, boolean isChecked) {
                        checkItems[which] = isChecked;
                    }
                }).setPositiveButton("确定", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        String result = "";
                        for (int i = 0; i < checkItems.length; i++) {
                            if (checkItems[i])
                                result += lesson[i] + " ";
                        }
                        Toast.makeText(getApplicationContext(), "你选择了:" + result, Toast.LENGTH_SHORT).show();
                    }
                }).create();
                alert.show();
            }
        });
    }
}
```