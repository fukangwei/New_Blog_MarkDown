---
title: LinkedList类
date: 2018-12-20 19:53:50
categories: Java
---
&emsp;&emsp;LinkedList其实也就是我们在数据结构中的链表，这种数据结构有如下特性：

- 分配内存空间不是必须是连续的。
- 插入、删除操作很快，只要修改前后指针就OK了，时间复杂度为`O(1)`。
- 访问比较慢，必须得从第一个元素开始遍历，时间复杂度为`O(n)`。

在Java中，LinkedList提供了丰富的方法，可以模拟链式队列，链式堆栈等数据结构，为用户带来了极大的方便。

### add

&emsp;&emsp;方法如下所示：

- `boolean add(E e)`：在链表后添加一个元素，如果成功，返回true，否则返回false。
- `void addFirst(E e)`：在链表头部插入一个元素。
- `addLast(E e)`：在链表尾部添加一个元素。
- `void add(int index, E element)`：在指定位置插入一个元素。

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.add("first");
        linkedList.add("second");
        linkedList.add("third");
        System.out.println(linkedList);
​
        linkedList.addFirst("addFirst");
        System.out.println(linkedList);
​
        linkedList.addLast("addLast");
        System.out.println(linkedList);
​
        linkedList.add(2, "addByIndex");
        System.out.println(linkedList);
    }
}
```

执行结果：

``` bash
[first, second, third]
[addFirst, first, second, third]
[addFirst, first, second, third, addLast]
[addFirst, first, addByIndex, second, third, addLast]
```

### remove

&emsp;&emsp;方法如下所示：

- `E remove()`：移除链表中第一个元素。
- `boolean remove(Object o)`：移除链表中指定的元素。
- `E remove(int index)`：移除链表中指定位置的元素。
- `E removeFirst()`：移除链表中第一个元素，与remove类似。
- `E removeLast()`：移除链表中最后一个元素。
- `boolean removeFirstOccurrence(Object o)`：移除链表中第一次出现的`Object o`元素。
- `boolean removeLastOccurrence(Object o)`：移除链表中最后一次出现的`Object o`元素。

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.add("first");
        linkedList.add("second");
        linkedList.add("second");
        linkedList.add("third");
        linkedList.add("four");
        linkedList.add("five");
        System.out.println(linkedList);
​
        linkedList.remove();
        System.out.println("remove: " + linkedList);
​
        linkedList.remove("second");
        System.out.println("remove(Object): " + linkedList);
​
        linkedList.remove("six");
        System.out.println("remove(Object) not exist: " + linkedList);
​
        linkedList.remove(2);
        System.out.println("remove(index): " + linkedList);
​
        linkedList.removeFirst();
        System.out.println("removeFirst: " + linkedList);
​
        linkedList.removeLast();
        System.out.println("removeLast:" + linkedList);
​
        System.out.println("----------------------------------");
        linkedList.clear();
​
        linkedList.add("first");
        linkedList.add("second");
        linkedList.add("first");
        linkedList.add("third");
        linkedList.add("first");
        linkedList.add("five");
        System.out.println(linkedList);
​
        linkedList.removeFirstOccurrence("first");
        System.out.println("removeFirstOccurrence: " + linkedList);
​
        linkedList.removeLastOccurrence("first");
        System.out.println("removeLastOccurrence: " + linkedList);
    }
}
```

执行结果：

``` bash
[first, second, second, third, four, five]
remove: [second, second, third, four, five]
remove(Object): [second, third, four, five]
remove(Object) not exist: [second, third, four, five]
remove(index): [second, third, five]
removeFirst: [third, five]
removeLast:[third]
----------------------------------
[first, second, first, third, first, five]
removeFirstOccurrence: [second, first, third, first, five]
removeLastOccurrence: [second, first, third, five]
```

### get

&emsp;&emsp;方法如下所示：

`E get(int index)`：按照下标获取元素。
`E getFirst()`：获取第一个元素。
`E getLast()`：获取最后一个元素。

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.add("first");
        linkedList.add("second");
        linkedList.add("second");
        linkedList.add("third");
        linkedList.add("four");
        linkedList.add("five");
        System.out.println(linkedList);
​
        linkedList.get(3);
        System.out.println("get(index): " + linkedList.get(3));
​
        linkedList.getFirst();
        System.out.println("getFirst: " + linkedList.getFirst());
​
        linkedList.getLast();
        System.out.println("getLast: " + linkedList.getLast());
​
        System.out.println(linkedList);
    }
}
```

执行结果：

``` bash
[first, second, second, third, four, five]
get(index): third
getFirst: first
getLast: five
[first, second, second, third, four, five]
```

### push、pop和poll

&emsp;&emsp;方法如下所示：

- `void push(E e)`：与addFirst一样，实际上它就是addFirst。
- `E pop()`：与removeFirst一样，实际上它就是removeFirst。
- `E poll()`：查询并移除第一个元素。

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.push("first");
        linkedList.push("second");
        linkedList.push("second");
        linkedList.push("third");
        linkedList.push("four");
        linkedList.push("five");
        System.out.println("linkedList: " + linkedList);
​
        System.out.println("pop: " + linkedList.pop());
        System.out.println("after pop: " + linkedList);
​
        System.out.println("poll: " + linkedList.poll());
        System.out.println("after poll: " + linkedList);
    }
}
```

执行结果：

``` bash
linkedList: [five, four, third, second, second, first]
pop: five
after pop: [four, third, second, second, first]
poll: four
after poll: [third, second, second, first]
```

如果链表为空，看看poll与pop之间的区别：

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        System.out.println("poll: " + linkedList.poll());
        System.out.println("pop: " + linkedList.pop());
    }
}
```

执行结果：

``` bash
poll: null
Exception in thread "main" java.util.NoSuchElementException
    at java.base/java.util.LinkedList.removeFirst(LinkedList.java:274)
    at java.base/java.util.LinkedList.pop(LinkedList.java:805)
    at LinkedListMethodsDemo.main(LinkedListMethodsDemo.java:8)
```

可以看出poll返回null，而pop则产生异常。

### peek

&emsp;&emsp;方法如下所示：

- `E peek()`：获取第一个元素，但是不移除。
- `E peekFirst()`：获取第一个元素，但是不移除。
- `E peekLast()`：获取最后一个元素，但是不移除。

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.push("first");
        linkedList.push("second");
        linkedList.push("second");
        linkedList.push("third");
        linkedList.push("four");
        linkedList.push("five");
        System.out.println("linkedList: " + linkedList);
​
        System.out.println("peek: " + linkedList.peek());
        System.out.println("peekFirst: " + linkedList.peekFirst());
        System.out.println("peekLast: " + linkedList.peekLast());
​
        System.out.println("linkedList: " + linkedList);
    }
}
```

如果没找到对应的元素，则统统输出null：

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        System.out.println("linkedList: " + linkedList);
        System.out.println("peek: " + linkedList.peek());
        System.out.println("peekFirst: " + linkedList.peekFirst());
        System.out.println("peekLast: " + linkedList.peekLast());
    }
}
```

执行结果：

``` bash
linkedList: []
peek: null
peekFirst: null
peekLast: null
```

### offer

&emsp;&emsp;方法如下所示：

- `boolean offer(E e)`：在链表尾部插入一个元素。
- `boolean offerFirst(E e)`：与addFirst一样，实际上它就是addFirst。
- `boolean offerLast(E e)`：与addLast一样，实际上它就是addLast。

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.push("first");
        linkedList.push("second");
        linkedList.push("second");
        linkedList.push("third");
        linkedList.push("four");
        linkedList.push("five");
        System.out.println("linkedList: " + linkedList);
​
        linkedList.offer("six");
        System.out.println("linkedList: " + linkedList);
​
        linkedList.offerFirst("zero");
        System.out.println("linkedList: " + linkedList);
​
        linkedList.offerLast("seven");
        System.out.println("linkedList: " + linkedList);
    }
}
```

执行结果：

``` bash
linkedList: [five, four, third, second, second, first]
linkedList: [five, four, third, second, second, first, six]
linkedList: [zero, five, four, third, second, second, first, six]
linkedList: [zero, five, four, third, second, second, first, six, seven]
```

### 其他

&emsp;&emsp;LinkedList中常用的方法基本都列出来了，当然还有一些其他的例子：

``` java
import java.util.LinkedList;
​
public class LinkedListMethodsDemo {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
​
        linkedList.push("first");
        linkedList.push("second");
        linkedList.push("second");
        linkedList.push("third");
        linkedList.push("four");
        linkedList.push("five");
        System.out.println("linkedList: " + linkedList);
​
        System.out.println("linkedList.contains(\"second\"): " + linkedList.contains("second"));
        System.out.println("linkedList.contains(\"six\"): " + linkedList.contains("six"));
        System.out.println("linkedList.element(): " + linkedList.element());
        System.out.println("linkedList: " + linkedList);
        System.out.println("linkedList.set(3, \"set\"): " + linkedList.set(3, "set"));
        System.out.println("linkedList: " + linkedList);
        System.out.println("linkedList.subList(2,4): " + linkedList.subList(2, 4));
        System.out.println("linkedList: " + linkedList);
    }
}
```

执行结果：

``` bash
linkedList: [five, four, third, second, second, first]
linkedList.contains("second"): true
linkedList.contains("six"): false
linkedList.element(): five
linkedList: [five, four, third, second, second, first]
linkedList.set(3, "set"): second
linkedList: [five, four, third, set, second, first]
linkedList.subList(2,4): [third, set]
linkedList: [five, four, third, set, second, first]
```