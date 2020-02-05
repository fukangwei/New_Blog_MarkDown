---
title: Collections类
categories: Java
abbrlink: 7d882df0
date: 2018-12-20 18:00:14
---
&emsp;&emsp;`Collections`是一个可以操作或返回集合的专用静态类。<!--more-->

### 排序操作

&emsp;&emsp;该操作主要针对`List`接口：

- `public static void reverse(List<?> list)`：反转指定`List`集合中元素的顺序。
- `public static void shuffle(List<?> list)`：对`List`中的元素进行随机排序(洗牌)。
- `public static void sort(List<T> list)`：对`List`里的元素根据自然升序排序。
- `public static void sort(List<T> list, Comparator<? super T> c)`：自定义比较器进行排序。
- `public static void swap(List<?> list, int i, int j)`：将指定`List`集合中的`i`处元素和`j`处元素进行交换
- `public static void rotate(List<?> list, int distance)`：将所有元素向右移位指定长度，如果`distance`等于`size`，那么结果不变。

代码实例如下：

``` java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
​
public class collections_test {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        list.add("D");
        list.add("E");
​
        Collections.reverse(list); /* 反转指定List集合中元素的顺序 */
        System.out.println("reverse后的顺序：" + list);
        Collections.shuffle(list); /* 对List中的元素进行随机排序(洗牌) */
        System.out.println("shuffle后的顺序：" + list);//每次都不一样
        Collections.sort(list); /* 对List里的元素根据自然升序排序 */
        System.out.println("sort后的顺序：" + list);
        Collections.swap(list, 1, 3); /* 将指定List集合中i处元素和j处元素进行交换 */
        System.out.println("swap后的顺序：" + list);
        /* 将所有元素向右移位指定长度，如果distance等于size，那么结果不变 */
        Collections.rotate(list, 2);
        System.out.println("rotate后的顺序：" + list);
    }
}
```

执行结果：

``` bash
reverse后的顺序：[E, D, C, B, A]
shuffle后的顺序：[C, E, A, B, D]
sort后的顺序：[A, B, C, D, E]
swap后的顺序：[A, D, C, B, E]
rotate后的顺序：[B, E, A, D, C]
```

### 查找和替换

&emsp;&emsp;该操作主要针对`Collection`接口：

- `public static void binarySearch(List<? extends Comparable<? super T>> list, T key)`：使用二分搜索法，以获得指定对象在`List`中的索引，前提是集合已经排序。
- `public static <T extends Object & Comparable<? super T>> T max(Collection<? extends T> coll)`：返回最大元素。
- `public static <T> T max(Collection<? extends T> coll,Comparator<? super T> comp)`：根据自定义比较器，返回最大元素。
- `public static <T extends Object & Comparable<? super T>> T min(Collection<? extends T> coll)`：返回最小元素。
- `public static <T> T min(Collection<? extends T> coll,Comparator<? super T> comp)`：根据自定义比较器，返回最小元素。
- `public static <T> void fill(List<? super T> list, T obj)`：使用指定元素替换指定列表中的所有元素。
- `public static int frequency(Collection<?> c, Object o)`：返回指定集合中指定对象出现的次数。
- `public static <T> boolean replaceAll(List list, T oldVal, T newVal)`：替换操作。

代码实例如下：

``` java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
​
public class collections_test {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        list.add("C");
        list.add("D");
        list.add("C");
        list.add("C");
        list.add("E");
​
        System.out.println("max：" + Collections.max(list));
        System.out.println("min：" + Collections.min(list));
        System.out.println("frequency：" + Collections.frequency(list, "C"));
​
        Collections.replaceAll(list, "C", "HanPang");
        System.out.println("replaceAll之后：" + list);
​
        /* 如果binarySearch的对象没有排序的话，搜索结果是不确定的 */
        System.out.println("binarySearch在sort之前：" + Collections.binarySearch(list, "E"));
        Collections.sort(list);
        /* sort之后，正确结果出来了 */
        System.out.println("排序后的结果：" + list);
        System.out.println("binarySearch在sort之后：" + Collections.binarySearch(list, "E"));
​
        Collections.fill(list, "^_^");
        System.out.println("fill：" + list);
    }
}
```

执行结果：

``` bash
max：E
min：A
frequency：4
replaceAll之后：[A, B, HanPang, HanPang, D, HanPang, HanPang, E]
binarySearch在sort之前：-3
排序后的结果：[A, B, D, E, HanPang, HanPang, HanPang, HanPang]
binarySearch在sort之后：3
fill：[^_^, ^_^, ^_^, ^_^, ^_^, ^_^, ^_^, ^_^]
```

### 其他方法

&emsp;&emsp;其他的方法如下：

- `public static boolean disjoint(Collection c1, Collection c2)`：如果两个指定`collection`中没有相同的元素，则返回`true`。
- `public static <T> boolean addAll(Collection<? super T> c, T... elements)`：一种方便的方式，将所有指定元素添加到指定`collection`中。
- `public static <T> Comparator<T> reverseOrder()`：返回一个比较器，它强行反转指定比较器的顺序。如果指定比较器为`null`，则此方法等同于`reverseOrder`。

代码实例如下：

``` java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
​
public class collections_test {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("悟空");
        list.add("悟能");
        list.add("悟净");

        List<String> list1 = new ArrayList<>();
        List<String> list2 = new ArrayList<>();
        Collections.addAll(list1, "林冲", "宋江", "吴用", "悟能");
        Collections.addAll(list2, "林黛玉", "贾宝玉", "薛宝钗");
​
        /* disjoint检查两个Collection是否存在交集 */
        boolean flag1 = Collections.disjoint(list, list1);
        boolean flag2 = Collections.disjoint(list, list2);
        System.out.println("list和list1交集结果：" + flag1);
        System.out.println("list和list2交集结果：" + flag2);
​
        /* 利用reverseOrder进行倒序 */
        Collections.sort(list1, Collections.reverseOrder());
        System.out.println(list1);
    }
}
```

执行结果：

``` bash
list和list1交集结果：false
list和list2交集结果：true
[林冲, 悟能, 宋江, 吴用]
```

### 设置不可变集合

&emsp;&emsp;`不可变`即为无法修改返回容器的内容，注意这里指的是无法直接通过`set`或者`add`方法修改容器内`reference`的指向，而不是禁止`reference`指向内容的修改。为什么要使用不可变集合？理由如下：

- 当对象被不可信的库调用时，不可变形式是安全的。
- 不可变对象被多个线程调用时，不存在竞态条件问题。
- 不可变集合不需要考虑变化，因此可以节省时间和空间，所有不可变的集合都比它们的可变形式有更好的内存利用率。
- 不可变对象因为固定不变，所以可以作为常量来安全使用。

相关方法如下：

- `emptyXxx`开头的方法：返回一个空的不可变的集合对象(`List`、`Map`和`Set`)。
- `singletonXxx`开头的方法：返回一个只包含指定对象的，不可变的集合对象。
- `unmodifiableXxx`开头的方法：返回指定集合对象的不可变视图。

代码实例如下：

``` java
import java.util.*;
​
public class collections_test {
    public static void main(String[] args) {
        List<?> list = Collections.emptyList(); /* 创建一个空的，不可改变的List对象 */
        System.out.println(list);
        /* 创建一个只有一个元素，且不可改变的map对象 */
        Map<String, String> map = Collections.singletonMap("name", "悟空");
        System.out.println(map);
        Map<String, Integer> scores = new HashMap<String, Integer>(); /* 创建一个普通的Map对象 */
        scores.put("英语", 85);
        scores.put("数学", 88);
        /* 返回普通的Map对象对应的不可变的版本 */
        Map<String, Integer> unmodifiaMap = Collections.unmodifiableMap(scores);
        /* 若在不可变的版本中继续添加数据，则会抛出UnsupportedOperationException异常 */
        // unmodifiaMap.put("Java", 99);
        System.out.println(unmodifiaMap);
    }
}
```

执行结果：

``` bash
[]
{name=悟空}
{数学=88, 英语=85}
```

### 同步控制

&emsp;&emsp;Collections工具类中提供了多个`synchronizedXxx`方法，该方法返回指定集合对象对应的同步对象，从而解决多线程并发访问集合时线程的安全问题。`HashSet`、`ArrayList`、`HashMap`都是线程不安全的，如果需要考虑同步，则使用这些方法。这些方法主要有`synchronizedSet`、`synchronizedSortedSet`、`synchronizedList`、`synchronizedMap`和`synchronizedSortedMap`。

- `public static Collection synchronizedCollection(Collection c)`：返回指定`Collection`支持的同步(线程安全的)`Collection`。
- `public static List synchronizedList(List list)`：返回指定列表支持的同步(线程安全的)列表。
- `public static Map synchronizedMap(Map m)`：返回由指定映射支持的同步(线程安全的)映射。
- `public static Set synchronizedSet(Set s)`：返回指定`Set`支持的同步(线程安全的)`Set`。

使用方法如下：

``` java
import java.util.*;
​
public class collections_test {
    public static void main(String[] args) {
        Collection c = Collections.synchronizedCollection(new ArrayList());
        List list = Collections.synchronizedList(new ArrayList());
        Set s = Collections.synchronizedSet(new HashSet());
        Map m = Collections.synchronizedMap(new HashMap());
    }
}
```