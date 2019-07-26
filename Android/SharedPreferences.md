---
title: SharedPreferences
date: 2019-02-10 09:54:55
categories: Android
---
&emsp;&emsp;在`Android`开发中，经常需要将少量简单类型数据保存在本地，例如用户设置。这些数据可能只是一两个字符串，一般选择使用`SharedPreferences`来保存。它使用`xml`文件存放数据，存放在`/data/data/<package name>/shared_prefs`目录下。

### 存储数据

&emsp;&emsp;保存数据一般分为四个步骤：

1. 使用`Activity`类的`getSharedPreferences`方法获得`SharedPreferences`对象。
2. 使用`SharedPreferences`接口的`edit`获得`SharedPreferences.Editor`对象。
3. 通过`SharedPreferences.Editor`接口的`putXXX`方法设置`key-value`对。
4. 通过`SharedPreferences.Editor`接口的`commit`方法保存`key-value`对。

注意，`putXXX`中`XXX`指的是数据类型，例如`int`、`boolean`、`float`、`long`、`String`、`StringSet`。

### 读取数据

&emsp;&emsp;读取数据一般分为两个步骤：

1. 使用`Activity`类的`getSharedPreferences`方法获得`SharedPreferences`对象。
2. 通过`SharedPreferences`对象的`getXXX`方法获取数据。

### 使用的方法

#### getSharedPreferences

&emsp;&emsp;该函数用于获取`SharedPreferences`对象：

``` java
public abstract SharedPreferences getSharedPreferences (String name, int mode);
```

根据`name`查找`SharedPreferences`，若已经存在，则获取；若不存在，则创建一个新的。参数`name`是命名；参数`mode`是模式，包括如下选项：

- `MODE_PRIVATE`：只能被自己的应用程序访问。
- `MODE_WORLD_READABLE`：除了自己访问外，还可以被其它应该程序读取。
- `MODE_WORLD_WRITEABLE`：除了自己访问外，还可以被其它应该程序读取和写入。

若该`Activity`只需要创建一个`SharedPreferences`对象的时候，可以使用`getPreferences`方法，不需要为`SharedPreferences`对象命名，只要传入参数`mode`即可：

``` java
public SharedPreferences getPreferences (int mode);
```

#### edit

&emsp;&emsp;该函数用于获取`Editor`对象(由`SharedPreferences`对象调用)：

``` java
abstract SharedPreferences.Editor edit();
```

#### putXXX

&emsp;&emsp;写入数据使用如下函数(由`Editor`对象调用)：

``` java
abstract SharedPreferences.Editor putBoolean (String key, boolean value); /* 写入boolean类型数据 */
abstract SharedPreferences.Editor putFloat (String key, float value); /* 写入float类型数据 */
abstract SharedPreferences.Editor putInt (String key, int value); /* 写入int类型数据 */
abstract SharedPreferences.Editor putLong (String key, long value); /* 写入long类型数据 */
abstract SharedPreferences.Editor putString (String key, String value); /* 写入String类型数据 */
abstract SharedPreferences.Editor putStringSet (String key, Set<String> values); /* 写入Set<String>类型数据 */
```

参数`key`是指定数据对应的`key`，`value`是指定的值。

#### remove

&emsp;&emsp;该函数用于移除指定`key`的数据(由`Editor`对象调用)：

``` java
abstract SharedPreferences.Editor remove (String key);
```

参数`key`是指定数据的`key`。

#### clear

&emsp;&emsp;该函数用于清空数据(由`Editor`对象调用)：

``` java
abstract SharedPreferences.Editor clear();
```

#### commit

&emsp;&emsp;该函数用于提交数据(由`Editor`对象调用)：

``` java
abstract boolean commit();
```

#### getXXX

&emsp;&emsp;读取数据(由`SharedPreferences`对象调用)使用如下函数：

``` java
abstract Map<String, ?> getAll(); /* 读取所有数据 */
abstract boolean getBoolean (String key, boolean defValue); /* 读取的数据为boolean类型 */
abstract float getFloat (String key, float defValue); /* 读取的数据为float类型 */
abstract int getInt (String key, int defValue); /* 读取的数据为int类型 */
abstract long getLong (String key, long defValue); /* 读取的数据为long类型 */
abstract String getString (String key, String defValue); /* 读取的数据为String类型 */
abstract Set<String> getStringSet (String key, Set<String> defValues); /* 读取的数据为Set<String>类型 */
```

参数`key`是指定数据的`key`；对于参数`defValue`，当读取不到指定的数据时，使用的默认值`defValue`。

### 示例代码

&emsp;&emsp;代码如下：

``` java
import android.content.SharedPreferences;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
​
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "SharedPreferencesTest";
    private final String PREFS_NAME = "MyPrefsFile"; /* 保存数据SharedPreferences文件的名字 */
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        saveUserInfo();
        getUserInfo();
        removeUserInfo();
        getUserInfo();
        clearUserInfo();
        getUserInfo();
    }
​
    private void saveUserInfo() { /* 保存用户信息 */
        SharedPreferences userInfo = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
        SharedPreferences.Editor editor = userInfo.edit(); /* 获取Editor */
        editor.putString("username", "一只猫的涵养"); /* 得到Editor后，写入需要保存的数据 */
        editor.putInt("age", 20);
        editor.commit(); /* 提交修改 */
        Log.i(TAG, "保存用户信息成功");
    }
​
    private void getUserInfo() { /* 读取用户信息 */
        SharedPreferences userInfo = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
        String username = userInfo.getString("username", null); /* 读取username */
        int age = userInfo.getInt("age", 0); /* 读取age */
        Log.i(TAG, "读取用户信息");
        Log.i(TAG, "username:" + username + "， age:" + age);
    }
​
    private void removeUserInfo() { /* 移除年龄数据 */
        SharedPreferences userInfo = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
        SharedPreferences.Editor editor = userInfo.edit(); /* 获取Editor */
        editor.remove("age");
        editor.commit();
        Log.i(TAG, "移除年龄数据");
    }
​
    private void clearUserInfo() { /* 清空数据 */
        SharedPreferences userInfo = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
        SharedPreferences.Editor editor = userInfo.edit(); /* 获取Editor */
        editor.clear();
        editor.commit();
        Log.i(TAG, "清空数据");
    }
}
```