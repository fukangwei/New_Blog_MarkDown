---
title: Hashtable类
categories: Java
date: 2018-12-25 18:48:25
---
&emsp;&emsp;`Hashtable`是原始的`java.util`的一部分，是一个`Dictionary`具体的实现。然而`Java 2`重构的`Hashtable`实现了`Map`接口，因此`Hashtable`现在集成到了集合框架中。它和`HashMap`类很相似，但是它支持同步。<!--more-->
&emsp;&emsp;像`HashMap`一样，`Hashtable`在哈希表中存储键值对。当使用一个哈希表时，要指定用作键的对象，以及要链接到该键的值。然后该键经过哈希处理，所得到的散列码被用作存储在该表中值的索引。
&emsp;&emsp;`Hashtable`定义了四个构造方法，第一个是默认构造方法：

``` java
Hashtable();
```

第二个构造函数创建指定大小的哈希表：

``` java
Hashtable(int size);
```

第三个构造方法创建了一个指定大小的哈希表，并且通过`fillRatio`指定填充比例。填充比例必须介于`0.0`和`1.0`之间，它决定了哈希表在重新调整大小之前的充满程度：

``` java
Hashtable(int size, float fillRatio);
```

第四个构造方法创建了一个以`M`中元素为初始化元素的哈希表。哈希表的容量被设置为`M`的两倍：

``` java
Hashtable(Map m);
```

&emsp;&emsp;`Hashtable`中除了从`Map`接口中定义的方法外，还定义了以下方法：

- `void clear()`：将此哈希表清空，使其不包含任何键。
- `Object clone()`：创建此哈希表的浅表副本。
- `boolean contains(Object value)`：测试此映射表中是否存在与指定值关联的键。
- `boolean containsKey(Object key)`：测试指定对象是否为此哈希表中的键。
- `boolean containsValue(Object value)`：如果此`Hashtable`将一个或多个键映射到此值，则返回`true`。
- `Enumeration elements()`：返回此哈希表中的值的枚举。
- `Object get(Object key)`：返回指定键所映射到的值，如果此映射不包含此键的映射，则返回`null`。
- `boolean isEmpty()`：测试此哈希表是否没有键映射到值。
- `Enumeration keys()`：返回此哈希表中的键的枚举。
- `Object put(Object key, Object value)`：将指定`key`映射到此哈希表中的指定`value`。
- `void rehash()`：增加此哈希表的容量，并在内部对其进行重组，以便更有效地容纳和访问其元素。
- `Object remove(Object key)`：从哈希表中移除该键及其相应的值。
- `int size()`：返回此哈希表中的键的数量。
- `String toString()`：返回此`Hashtable`对象的字符串表示形式，其形式为`ASCII`字符`,(空格)`(逗号加空格)分隔开的、括在括号中的一组条目。

``` java
import java.util.*;

public class HashTableDemo {
    public static void main(String args[]) {
        /* Create a hash map */
        Hashtable balance = new Hashtable();
        Enumeration names;
        String str;
        double bal;

        balance.put("Zara", 3434.34);
        balance.put("Mahnaz", 123.22);
        balance.put("Ayan", 1378.00);
        balance.put("Daisy", 99.22);
        balance.put("Qadir", -19.08);

        /* Show all balances in hash table */
        names = balance.keys();
        while (names.hasMoreElements()) {
            str = (String) names.nextElement();
            System.out.println(str + ": " + balance.get(str));
        }

        System.out.println();
        /* Deposit 1,000 into Zara's account */
        bal = ((Double) balance.get("Zara")).doubleValue();
        balance.put("Zara", bal + 1000);
        System.out.println("Zara's new balance: " + balance.get("Zara"));
    }
}
```

执行结果：

``` bash
Qadir: -19.08
Zara: 3434.34
Mahnaz: 123.22
Daisy: 99.22
Ayan: 1378.0

Zara's new balance: 4434.34
```