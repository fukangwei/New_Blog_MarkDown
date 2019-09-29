---
title: Bitset类
categories: Java
abbrlink: f417f3c6
date: 2018-12-26 09:49:13
---
&emsp;&emsp;`Bitset`类创建一种特殊类型的数组来保存位值。`BitSet`的数组大小会随需要增加，这和`位向量`(`vector of bits`)类似。它是一个传统的类，但在`Java 2`中被完全重新设计。
&emsp;&emsp;`BitSet`定义了两个构造方法，第一个构造方法创建一个默认的对象：

``` java
BitSet()
```

第二个方法允许用户指定初始大小，所有位初始化为`0`：

``` java
BitSet(int size)
```

&emsp;&emsp;`BitSet`实现了`Cloneable`接口中定义的方法：

- `void and(BitSet set)`：对此目标位和参数位`set`执行逻辑与操作。
- `void andNot(BitSet set)`：清除此`BitSet`中所有的位，其相应的位在指定的`BitSet`中设置。
- `int cardinality()`：返回此`BitSet`中设置为`true`的位数。
- `void clear()`：将此`BitSet`中的所有位设置为`false`。
- `void clear(int index)`：将索引指定处的位设置为`false`。
- `void clear(int startIndex, int endIndex)`：将指定的`fromIndex`(包括)到指定的`toIndex`(不包括)范围内的位设置为`false`。
- `Object clone()`：复制此`BitSet`，生成一个与之相等的新`BitSet`。
- `boolean equals(Object bitSet)`：将此对象与指定的对象进行比较。
- `void flip(int index)`：将指定索引处的位设置为其当前值的补码。
- `void flip(int startIndex, int endIndex)`：将指定的`fromIndex`(包括)到指定的`toIndex`(不包括)范围内的每个位设置为其当前值的补码。
- `boolean get(int index)`：返回指定索引处的位值。
- `BitSet get(int startIndex, int endIndex)`：返回一个新的`BitSet`，它由此`BitSet`中从`fromIndex`(包括)到`toIndex`(不包括)范围内的位组成。
- `int hashCode()`：返回此位`set`的哈希码值。
- `boolean intersects(BitSet bitSet)`：如果指定的`BitSet`中有设置为`true`的位，并且在此`BitSet`中也将其设置为`true`，则返回`ture`。
- `boolean isEmpty()`：如果此`BitSet`中没有包含任何设置为`true`的位，则返回`ture`。
- `int length()`：返回此`BitSet`的`逻辑大小`，即`BitSet`中最高设置位的索引加`1`。
- `int nextClearBit(int startIndex)`：返回第一个设置为`false`的位的索引，这发生在指定的起始索引或之后的索引上。
- `int nextSetBit(int startIndex)`：返回第一个设置为`true`的位的索引，这发生在指定的起始索引或之后的索引上。
- `void or(BitSet bitSet)`：对此位`set`和参数位`set`执行逻辑或操作。
- `void set(int index)`：将指定索引处的位设置为`true`。
- `void set(int index, boolean v)`：将指定索引处的位设置为指定的值。
- `void set(int startIndex, int endIndex)`：将指定的`fromIndex`(包括)到指定的`toIndex`(不包括)范围内的位设置为`true`。
- `void set(int startIndex, int endIndex, boolean v)`：将指定的`fromIndex`(包括)到指定的`toIndex`(不包括)范围内的位设置为指定的值。
- `int size()`：返回此`BitSet`表示位值时实际使用空间的位数。
- `String toString()`：返回此位`set`的字符串表示形式。
- `void xor(BitSet bitSet)`：对此位`set`和参数位`set`执行逻辑异或操作。

代码如下：

``` java
import java.util.BitSet;
​
public class BitSetDemo {​
    public static void main(String args[]) {
        BitSet bits1 = new BitSet(16);
        BitSet bits2 = new BitSet(16);
​
        for (int i = 0; i < 16; i++) { /* set some bits */
            if ((i % 2) == 0) bits1.set(i);
            if ((i % 5) != 0) bits2.set(i);
        }
​
        System.out.print("Initial pattern in bits1: ");
        System.out.println(bits1);
        System.out.print("Initial pattern in bits2: ");
        System.out.println(bits2);
​
        bits2.and(bits1); /* AND bits */
        System.out.print("bits2 AND bits1: ");
        System.out.println(bits2);
​
        bits2.or(bits1); /* OR bits */
        System.out.print("bits2 OR bits1: ");
        System.out.println(bits2);
​
        bits2.xor(bits1); /* XOR bits */
        System.out.print("bits2 XOR bits1: ");
        System.out.println(bits2);
    }
}
```

执行结果：

``` bash
Initial pattern in bits1: {0, 2, 4, 6, 8, 10, 12, 14}
Initial pattern in bits2: {1, 2, 3, 4, 6, 7, 8, 9, 11, 12, 13, 14}
bits2 AND bits1: {2, 4, 6, 8, 12, 14}
bits2 OR bits1: {0, 2, 4, 6, 8, 10, 12, 14}
bits2 XOR bits1: {}
```