---
title: Properties类
date: 2018-12-21 20:41:14
categories: Java
---
&emsp;&emsp;`Properties`继承于`Hashtable`，表示一个持久的属性集，属性列表中每个键及其对应值都是一个字符串。`Properties`类被许多`Java`类使用，例如在获取环境变量时，它就作为`System.getProperties`方法的返回值。
&emsp;&emsp;`Properties`定义如下实例变量，这个变量持有一个`Properties`对象相关的默认属性列表：

``` java
Properties defaults;
```

&emsp;&emsp;`Properties`类定义了两个构造方法，第一个构造方法没有默认值：

``` java
Properties();
```

第二个构造方法使用`propDefault`作为默认值：

``` java
Properties(Properties propDefault);
```

&emsp;&emsp;除了从`Hashtable`中继承的方法，`Properties`还定义了以下方法：

- `String getProperty(String key)`：用指定的键在此属性列表中搜索属性。
- `String getProperty(String key, String defaultProperty)`：用指定的键在此属性列表中搜索属性。
- `void list(PrintStream streamOut)`：将属性列表输出到指定的输出流。
- `void list(PrintWriter streamOut)`：将属性列表输出到指定的输出流。
- `void load(InputStream streamIn) throws IOException`：从输入流中读取属性列表(键和元素对)。
- `Enumeration propertyNames()`：按简单的面向行的格式从输入字符流中读取属性列表(键和元素对)。
- `Object setProperty(String key, String value)`：调用`Hashtable`的方法`put`。
- `void store(OutputStream streamOut, String description)`：以适合使用`load(InputStream)`方法加载到`Properties`表中的格式，将此`Properties`表中的属性列表(键和元素对)写入输出流。

代码如下：

``` java
import java.util.*;
​
public class PropDemo {
    public static void main(String args[]) {
        Properties capitals = new Properties();
        Set states;
        String str;
​
        capitals.put("Illinois", "Springfield");
        capitals.put("Missouri", "Jefferson City");
        capitals.put("Washington", "Olympia");
        capitals.put("California", "Sacramento");
        capitals.put("Indiana", "Indianapolis");
​
        /* Show all states and capitals in hashtable */
        states = capitals.keySet(); /* get set-view of keys */
        Iterator itr = states.iterator();

        while (itr.hasNext()) {
            str = (String) itr.next();
            System.out.println("The capital of " + str + " is " + capitals.getProperty(str) + ".");
        }

        System.out.println();
​
        /* look for state not in list -- specify default */
        str = capitals.getProperty("Florida", "Not Found");
        System.out.println("The capital of Florida is " + str + ".");
    }
}
```

执行结果：

``` bash
The capital of Indiana is Indianapolis.
The capital of Illinois is Springfield.
The capital of Missouri is Jefferson City.
The capital of California is Sacramento.
The capital of Washington is Olympia.
​
The capital of Florida is Not Found.
```

&emsp;&emsp;`Java`的`Iterator`功能比较简单，并且只能单向移动：

- 使用方法`iterator`要求容器返回一个`Iterator`。注意，`iterator`方法是`java.lang.Iterable`接口，被`Collection`继承。
- 使用`next`获得序列中的下一个元素。第一次调用`Iterator`的`next`方法时，它返回序列的第一个元素。
- 使用`hasNext`检查序列中是否还有元素。
- 使用`remove`将迭代器新返回的元素删除。