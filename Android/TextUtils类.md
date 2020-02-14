---
title: TextUtils类
categories: Android
abbrlink: 2f683b3d
date: 2018-12-28 11:55:06
---
&emsp;&emsp;对于字符串处理，`Android`为我们提供了一个简单实用的`TextUtils`类，其主要功能如下：<!--more-->

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.text.TextUtils;
import android.util.Log;
import java.util.LinkedList;
import java.util.List;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        String a = "Android";
        String b = " Studio";
        String c = "012345";
        String d = " \n\t \b";
        String e = "11-233-we-7d-fv";
        String f = "<html>\n<body>\n<h1>My First Heading</h1>\n<p>My first paragraph.</p>\n</body>\n</html>";
        Log.e("tag", TextUtils.isEmpty(a) + ""); /* 字符串是否为null或为空 */
        Log.e("tag", TextUtils.isDigitsOnly(c) + ""); /* 字符串是否全是数字 */
        Log.e("tag", TextUtils.isGraphic(d) + ""); /* 字符串是否含有可打印的字符 */
        Log.e("tag", TextUtils.concat(a, b) + ""); /* 拼接多个字符串 */
        Log.e("tag", TextUtils.getTrimmedLength(b) + ""); /* 去掉字符串前后两端空格(相当于trim)之后的长度 */
        Log.e("tag", TextUtils.getReverse(b, 0, b.length()) + ""); /* 在字符串中，将start和end之间字符的逆序 */
        List<String> list = new LinkedList<String>();
        list.add("I");
        list.add("love");
        list.add("you");
        Log.e("tag", TextUtils.join("-", list)); /* 在数组中，每个元素之间使用“-”来连接 */
        String[] arr = TextUtils.split(e, "-"); /* 以“-”来分组 */
​
        for (int i = 0; i < arr.length; i++) {
            Log.e("tag", arr[i]);
        }
​
        Log.e("tag", TextUtils.htmlEncode(f) + ""); /* 使用html编码字符串 */
    }
}
```