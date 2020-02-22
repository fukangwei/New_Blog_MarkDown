---
title: Map接口
categories: Java
date: 2018-12-21 20:11:33
---
&emsp;&emsp;在`Map`接口中，键和值一一映射，可以通过键来获取值。<!--more-->

- 给定一个键和一个值，你可以将该值存储在一个`Map`对象。之后，你可以通过键来访问对应的值。
- 当访问的值不存在的时候，方法就会抛出一个`NoSuchElementException`异常。
- 当对象的类型和`Map`里元素类型不兼容的时候，就会抛出一个`ClassCastException`异常。
- 当在不允许使用`Null`对象的`Map`中使用`Null`对象，会抛出一个`NullPointerException`异常。
- 当尝试修改一个只读的`Map`时，会抛出一个`UnsupportedOperationException`异常。

支持的方法如下：

- `void clear()`：从此映射中移除所有映射关系(可选操作)。
- `boolean containsKey(Object k)`：如果此映射包含指定键的映射关系，则返回`true`。
- `boolean containsValue(Object v)`：如果此映射将一个或多个键映射到指定值，则返回`true`。
- `Set entrySet()`：返回此映射中包含的映射关系的`Set`视图。
- `boolean equals(Object obj)`：比较指定的对象与此映射是否相等。
- `Object get(Object k)`：返回指定键所映射的值，如果此映射不包含该键的映射关系，则返回`null`。
- `int hashCode()`：返回此映射的哈希码值。
- `boolean isEmpty()`：如果此映射未包含键值映射关系，则返回`true`。
- `Set keySet()`：返回此映射中包含的键的`Set`视图。
- `Object put(Object k, Object v)`：将指定的值与此映射中的指定键关联(可选操作)。
- `void putAll(Map m)`：从指定映射中将所有映射关系复制到此映射中(可选操作)。
- `Object remove(Object k)`：如果存在一个键的映射关系，则将其从此映射中移除(可选操作)。
- `int size()`：返回此映射中的键值映射关系数。
- `Collection values()`：返回此映射中包含的值的`Collection`视图。

代码如下：

``` java
import java.util.*;

public class CollectionsDemo {
    public static void main(String[] args) {
        Map m1 = new HashMap();
        m1.put("Zara", "8");
        m1.put("Mahnaz", "31");
        m1.put("Ayan", "12");
        m1.put("Daisy", "14");
        System.out.print("Map Elements: ");
        System.out.println(m1);
    }
}
```

执行结果：

``` bash
Map Elements: {Daisy=14, Ayan=12, Zara=8, Mahnaz=31}
```