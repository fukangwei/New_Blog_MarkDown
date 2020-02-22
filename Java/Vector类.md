---
title: Vector类
categories: Java
date: 2018-12-24 15:22:17
---
&emsp;&emsp;`Vector`类实现了一个动态数组，和`ArrayList`类似，但两者是不同的：<!--more-->

- `Vector`是同步访问的。
- `Vector`包含了许多传统的方法，这些方法不属于集合框架。
- `Vector`主要用在事先不知道数组的大小，或者只是需要一个可以改变大小的数组的情况。

&emsp;&emsp;`Vector`类支持`4`种构造方法，第一种构造方法创建一个默认的向量，默认大小为`10`：

``` java
Vector();
```

第二种构造方法创建指定大小的向量：

``` java
Vector(int size);
```

第三种构造方法创建了一个向量，其初始容量是由`size`和由`incr`指定的增量决定，增量表示向量每次增加的元素数目：

``` java
Vector(int size, int incr);
```

第四种构造方法创建一个包含集合`c`元素的向量：

``` java
Vector(Collection c);
```

&emsp;&emsp;除了从父类继承的方法外，`Vector`还定义了以下方法：

- `void add(int index, Object element)`：在此向量的指定位置插入指定的元素。
- `boolean add(Object o)`：将指定元素添加到此向量的末尾。
- `boolean addAll(Collection c)`：将指定`Collection`中的所有元素添加到此向量的末尾，按照指定`collection`的迭代器所返回的顺序添加这些元素。
- `boolean addAll(int index, Collection c)`：在指定位置将指定`Collection`中的所有元素插入到此向量中。
- `void addElement(Object obj)`：将指定的组件添加到此向量的末尾，将其大小增加`1`。
- `int capacity()`：返回此向量的当前容量。
- `void clear()`：从此向量中移除所有元素。
- `Object clone()`：返回向量的一个副本。
- `boolean contains(Object elem)`：如果此向量包含指定的元素，则返回`true`。
- `boolean containsAll(Collection c)`：如果此向量包含指定`Collection`中的所有元素，则返回`true`。
- `void copyInto(Object[] anArray)`：将此向量的组件复制到指定的数组中。
- `Object elementAt(int index)`：返回指定索引处的组件。
- `Enumeration elements()`：返回此向量的组件的枚举。
- `void ensureCapacity(int minCapacity)`：增加此向量的容量(如有必要)，以确保其至少能够保存最小容量参数指定的组件数。
- `boolean equals(Object o)`：比较指定对象与此向量的相等性。
- `Object firstElement()`：返回此向量的第一个组件(位于索引`0`)处的项。
- `Object get(int index)`：返回向量中指定位置的元素。
- `int hashCode()`：返回此向量的哈希码值。
- `int indexOf(Object elem)`：返回此向量中第一次出现的指定元素的索引，如果此向量不包含该元素，则返回`-1`。
- `int indexOf(Object elem, int index)`：返回此向量中第一次出现的指定元素的索引，从`index`处正向搜索，如果未找到该元素，则返回`-1`。
- `void insertElementAt(Object obj, int index)`：将指定对象作为此向量中的组件插入到指定的`index`处。
- `boolean isEmpty()`：测试此向量是否不包含组件。
- `Object lastElement()`：返回此向量的最后一个组件。
- `int lastIndexOf(Object elem)`：返回此向量中最后一次出现的指定元素的索引；如果此向量不包含该元素，则返回`-1`。
- `int lastIndexOf(Object elem, int index)`：返回此向量中最后一次出现的指定元素的索引，从`index`处逆向搜索，如果未找到该元素，则返回`-1`。
- `Object remove(int index)`：移除此向量中指定位置的元素。
- `boolean remove(Object o)`：移除此向量中指定元素的第一个匹配项，如果向量不包含该元素，则元素保持不变。
- `boolean removeAll(Collection c)`：从此向量中移除包含在指定`Collection`中的所有元素。
- `void removeAllElements()`：从此向量中移除全部组件，并将其大小设置为`0`。
- `boolean removeElement(Object obj)`：从此向量中移除变量的第一个(索引最小的)匹配项。
- `void removeElementAt(int index)`：删除指定索引处的组件。
- `protected void removeRange(int fromIndex, int toIndex)`：从此List中移除其索引位于`fromIndex`(包括)与`toIndex`(不包括)之间的所有元素。
- `boolean retainAll(Collection c)`：在此向量中仅保留包含在指定`Collection`中的元素。
- `Object set(int index, Object element)`：用指定的元素替换此向量中指定位置处的元素。
- `void setElementAt(Object obj, int index)`：将此向量指定`index`处的组件设置为指定的对象。
- `void setSize(int newSize)`：设置此向量的大小。
- `int size()`：返回此向量中的组件数。
- `List subList(int fromIndex, int toIndex)`：返回此`List`的部分视图，元素范围为从`fromIndex`(包括)到`toIndex`(不包括)。
- `Object[] toArray()`：返回一个数组，包含此向量中以恰当顺序存放的所有元素。
- `Object[] toArray(Object[] a)`：返回一个数组，包含此向量中以恰当顺序存放的所有元素，数组的运行时类型为指定数组的类型。
- `String toString()`：返回此向量的字符串表示形式，其中包含每个元素的`String`表示形式。
- `void trimToSize()`：对此向量的容量进行微调，使其等于向量的当前大小。

``` java
import java.util.*;

public class VectorDemo {
    public static void main(String args[]) {
        /* initial size is 3, increment is 2 */
        Vector v = new Vector(3, 2);
        System.out.println("Initial size: " + v.size());
        System.out.println("Initial capacity: " + v.capacity());
        v.addElement(1);
        v.addElement(2);
        v.addElement(3);
        v.addElement(4);
        System.out.println("Capacity after four additions: " + v.capacity());

        v.addElement(5.45);
        System.out.println("Current capacity: " + v.capacity());
        v.addElement(6.08);
        v.addElement(7);
        System.out.println("Current capacity: " + v.capacity());
        v.addElement(9.4);
        v.addElement(10);
        System.out.println("Current capacity: " + v.capacity());
        v.addElement(11);
        v.addElement(12);
        System.out.println("First element: " + v.firstElement());
        System.out.println("Last element: " + v.lastElement());

        if (v.contains(3))
            System.out.println("Vector contains 3.");

        /* enumerate the elements in the vector */
        Enumeration vEnum = v.elements();
        System.out.println("\nElements in vector:");

        while (vEnum.hasMoreElements())
            System.out.print(vEnum.nextElement() + " ");

        System.out.println();
    }
}
```

执行结果：

``` bash
Initial size: 0
Initial capacity: 3
Capacity after four additions: 5
Current capacity: 5
Current capacity: 7
Current capacity: 9
First element: 1
Last element: 12
Vector contains 3.

Elements in vector:
1 2 3 4 5.45 6.08 7 9.4 10 11 12
```