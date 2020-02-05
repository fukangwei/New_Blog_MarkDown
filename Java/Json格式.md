---
title: Json格式
categories: Java
abbrlink: cbe7ec5
date: 2018-12-21 14:38:45
---
&emsp;&emsp;代码如下：<!--more-->

``` java
import org.json.JSONArray;
import org.json.JSONObject;
​
public class test {
    public static void main(String[] args) {
        /* 初始化JSONObject(方法一) */
        JSONObject jsonObject1 = new JSONObject();
        jsonObject1.put("Name", "Tom");
        jsonObject1.put("age", "11");
​
        /* 初始化JSONObject(方法二) */
        JSONObject jsonObject2 = new JSONObject("{'Name':'Tom','age':'11'}");
​
        /* 初始化JSONArray(方法一) */
        JSONArray jsonArray1 = new JSONArray();
        jsonArray1.put("abc");
        jsonArray1.put("xyz");
​
        /* 初始化JSONArray(方法二) */
        JSONArray jsonArray2 = new JSONArray("['abc','xyz']");
​
        System.out.println("jsonObject1:" + "\r" + jsonObject1.toString());
        System.out.println("jsonObject2:" + "\r" + jsonObject2.toString());
        System.out.println("jsonArray1:" + "\r" + jsonArray1.toString());
        System.out.println("jsonArray2:" + "\r" + jsonArray2.toString());
    }
}
```

执行结果：

``` bash
{"age":"11","Name":"Tom"}
{"age":"11","Name":"Tom"}
["abc","xyz"]
["abc","xyz"]
```

---

&emsp;&emsp;解析`Object`之一：

``` json
{"url":"http://www.cnblogs.com/qianxudetianxia"}
```

解析方法：

``` java
import org.json.JSONObject;
​
public class test {
    public static void main(String[] args) {
        String jsonString = "{'url':'http://www.cnblogs.com/qianxudetianxia'}";
        JSONObject demoJson = new JSONObject(jsonString);
        String url = demoJson.getString("url");
        System.out.println(url);
    }
}
```

执行结果：

``` bash
http://www.cnblogs.com/qianxudetianxia
```

&emsp;&emsp;解析`Object`之二：

``` json
{"name":"android","version":"1.2.3"}
```

解析方法：

``` java
import org.json.JSONObject;
​
public class test {
    public static void main(String[] args) {
        String jsonString = "{'name':'android','version':'1.2.3'}";
        JSONObject demoJson = new JSONObject(jsonString);
        String name = demoJson.getString("name");
        String version = demoJson.getString("version");
        System.out.println("name: " + name + ", version: " + version);
    }
}
```

&emsp;&emsp;解析`Array`之三：

``` json
{"number":[1,2,3]}
```

解析方法：

``` java
import org.json.JSONArray;
import org.json.JSONObject;
​
public class test {
    public static void main(String[] args) {
        String jsonString = "{'number':[1,2,3]}";
        JSONObject demoJson = new JSONObject(jsonString);
        JSONArray numberList = demoJson.getJSONArray("number");
​
        for (int i = 0; i < numberList.length(); i++) {
            /* 因为数组中的类型为int，所以为getInt，而getString、getLong同理 */
            System.out.println(numberList.getInt(i));
        }
    }
}
```

执行结果：

``` bash
1
2
3
```

&emsp;&emsp;解析`Array`之四：

``` json
{"number":[[1], [2], [3]]}
```

解析方法：

``` java
import org.json.JSONArray;
import org.json.JSONObject;
​
public class test {
    public static void main(String[] args) {
        String jsonString = "{'number':[[1],[2],[3]]}";
        /* 嵌套数组遍历 */
        JSONObject demoJson = new JSONObject(jsonString);
        JSONArray numberList = demoJson.getJSONArray("number");
        for (int i = 0; i < numberList.length(); i++) {
            /* 获取数组中的数组 */
            System.out.println(numberList.getJSONArray(i).getInt(0));
        }
    }
}
```

执行结果：

``` bash
1
2
3
```

&emsp;&emsp;解析`Object`和`Array`：

``` json
{"mobile":[{"name":"android"},{"name":"iphone"}]}
```

解析方法：

``` java
import org.json.JSONArray;
import org.json.JSONObject;
​
public class test {
    public static void main(String[] args) {
        String jsonString = "{'mobile':[{'name':'android'},{'name':'iphone'}]}";
        JSONObject demoJson = new JSONObject(jsonString);
        JSONArray numberList = demoJson.getJSONArray("mobile");
        for (int i = 0; i < numberList.length(); i++) {
            System.out.println(numberList.getJSONObject(i).getString("name"));
        }
    }
}
```

---

### JSONException

&emsp;&emsp;public class `JSONException`, extends `Exception`:

``` java
java.lang.Object
    |->  java.lang.Throwable
        |->  java.lang.Exception
            |->  org.json.JSONException
```

Thrown to indicate a problem with the `JSON API`. Such problems include:

- Attempts to parse or construct malformed documents.
- Use of null as a name.
- Use of numeric types not available to `JSON`, such as `NaNs` or `infinities`.
- Lookups using an out of range index or nonexistent name.
- Type mismatches on lookups.

Although this is a checked exception, it is rarely recoverable. Most callers should simply wrap this exception in an unchecked exception and rethrow:

``` java
public JSONArray toJSONObject() {
    try {
        JSONObject result = new JSONObject();
        ...
    } catch(JSONException e){
        throw new RuntimeException(e);
    }
}
```