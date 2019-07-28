---
title: synchronized的用法
date: 2018-12-20 21:17:28
categories: Java
---
&emsp;&emsp;`synchronized`是`Java`中的关键字，它是一种同步锁，修饰的对象有以下几种：

- 修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号`{}`括起来的代码，作用的对象是调用这个代码块的对象。
- 修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是调用这个方法的对象。
- 修改一个静态的方法，其作用的范围是整个静态方法，作用的对象是这个类的所有对象。
- 修改一个类，其作用的范围是`synchronized`后面括号括起来的部分，作用的对象是这个类的所有对象。

### 修饰一个代码块

#### synchronized的用法

&emsp;&emsp;一个线程访问一个对象中的`synchronized(this)`同步代码块时，其他试图访问该对象的线程将被阻塞。
&emsp;&emsp;`SyncThread.java`如下：

``` java
class SyncThread implements Runnable {
    private static int count;
​
    public SyncThread() {
        count = 0;
    }
​
    public void run() {
        synchronized (this) {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + " : " + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

`test.java`如下：

``` java
public class test {
    public static void main(String[] args) {
        SyncThread syncThread = new SyncThread();
        Thread thread1 = new Thread(syncThread, "SyncThread1");
        Thread thread2 = new Thread(syncThread, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
```

执行结果：

``` bash
SyncThread1 : 0
SyncThread1 : 1
SyncThread1 : 2
SyncThread1 : 3
SyncThread1 : 4
SyncThread2 : 5
SyncThread2 : 6
SyncThread2 : 7
SyncThread2 : 8
SyncThread2 : 9
```

当两个并发线程(`thread1`和`thread2`)访问同一个对象(`syncThread`)中的`synchronized`代码块时，在同一时刻只能有一个线程得到执行，另一个线程受阻塞，必须等待当前线程执行完这个代码块以后，才能执行该代码块。`Thread1`和`thread2`是互斥的，因为在执行`synchronized`代码块时会锁定当前的对象，只有执行完该代码块才能释放该对象锁，下一个线程才能执行并锁定该对象。
&emsp;&emsp;我们再把`SyncThread`的调用稍微改一下：

``` java
public class test {
    public static void main(String[] args) {
        Thread thread1 = new Thread(new SyncThread(), "SyncThread1");
        Thread thread2 = new Thread(new SyncThread(), "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
```

执行结果：

``` bash
SyncThread1 : 0
SyncThread2 : 1
SyncThread1 : 2
SyncThread2 : 3
SyncThread1 : 5
SyncThread2 : 4
SyncThread2 : 6
SyncThread1 : 7
SyncThread1 : 8
SyncThread2 : 9
```

不是说一个线程执行`synchronized`代码块时其它的线程受阻塞吗？为什么上面的例子中`thread1`和`thread2`同时在执行。这是因为`synchronized`只锁定对象，每个对象只有一个锁(`lock`)与之相关联，而上面的代码等同于下面这段代码：

``` java
public class test {
    public static void main(String[] args) {
        SyncThread syncThread1 = new SyncThread();
        SyncThread syncThread2 = new SyncThread();
        Thread thread1 = new Thread(syncThread1, "SyncThread1");
        Thread thread2 = new Thread(syncThread2, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
```

执行结果：

``` bash
SyncThread1 : 0
SyncThread2 : 1
SyncThread1 : 2
SyncThread2 : 3
SyncThread1 : 4
SyncThread2 : 5
SyncThread1 : 6
SyncThread2 : 7
SyncThread1 : 8
SyncThread2 : 9
```

这时创建了两个`SyncThread`的对象，即`syncThread1`和`syncThread2`。线程`thread1`执行的是`syncThread1`对象中的`synchronized`代码(`run`)，而线程`thread2`执行的是`syncThread2`对象中的`synchronized`代码(`run`)。我们知道`synchronized`锁定的是对象，这时会有两把锁分别锁定`syncThread1`对象和`syncThread2`对象，而这两把锁是互不干扰的，不形成互斥，所以两个线程可以同时执行。

#### 多个线程访问synchronized和非synchronized代码块

&emsp;&emsp;当一个线程访问对象的一个`synchronized(this)`同步代码块时，另一个线程仍然可以访问该对象中的非`synchronized(this)`同步代码块。
&emsp;&emsp;`Counter.java`如下：

``` java
class Counter implements Runnable {
    private int count;
​
    public Counter() {
        count = 0;
    }
​
    public void countAdd() {
        synchronized (this) {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + " : " + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
​
    /* 非synchronized代码块，未对count进行读写操作，所以可以不用synchronized */
    public void printCount() {
        for (int i = 0; i < 5; i++) {
            try {
                System.out.println(Thread.currentThread().getName() + " count : " + count);
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
​
    public void run() {
        String threadName = Thread.currentThread().getName();

        if (threadName.equals("A")) {
            countAdd();
        } else if (threadName.equals("B")) {
            printCount();
        }
    }
}
```

`test.java`如下：

``` java
public class test {
    public static void main(String[] args) {
        Counter counter = new Counter();
        Thread thread1 = new Thread(counter, "A");
        Thread thread2 = new Thread(counter, "B");
        thread1.start();
        thread2.start();
    }
}
```

执行结果：

``` bash
A : 0
B count : 1
A : 1
B count : 2
A : 2
B count : 3
A : 3
B count : 4
A : 4
B count : 5
```

上面代码中，`countAdd`是`synchronized`的，而`printCount`是非`synchronized`的。从上面的结果可以看出，一个线程访问一个对象的`synchronized`代码块时，别的线程可以访问该对象的非`synchronized`代码块而不受阻塞。

#### 指定要给某个对象加锁

&emsp;&emsp;`Account.java`如下：

``` java
class Account { /* 银行账户类 */
    String name;
    float amount;
​
    public Account(String name, float amount) {
        this.name = name;
        this.amount = amount;
    }
​
    public void deposit(float amt) { /* 存钱 */
        amount += amt;

        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
​
    public void withdraw(float amt) { /* 取钱 */
        amount -= amt;

        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
​
    public float getBalance() {
        return amount;
    }
}
```

`AccountOperator.java`如下：

``` java
class AccountOperator implements Runnable { /* 账户操作类 */
    private Account account;
​
    public AccountOperator(Account account) {
        this.account = account;
    }
​
    public void run() {
        synchronized (account) {
            account.deposit(500);
            account.withdraw(500);
            System.out.println(Thread.currentThread().getName() + " : " + account.getBalance());
        }
    }
}
```

`test.java`如下：

``` java
public class test {
    public static void main(String[] args) {
        Account account = new Account("zhang san", 10000.0f);
        AccountOperator accountOperator = new AccountOperator(account);
​
        final int THREAD_NUM = 5;
        Thread threads[] = new Thread[THREAD_NUM];
        for (int i = 0; i < THREAD_NUM; i++) {
            threads[i] = new Thread(accountOperator, "Thread" + i);
            threads[i].start();
        }
    }
}
```

执行结果：

``` bash
Thread0 : 10000.0
Thread2 : 10000.0
Thread3 : 10000.0
Thread1 : 10000.0
Thread4 : 10000.0
```

在`AccountOperator`类中的`run`方法里，我们用`synchronized`给`account`对象加了锁。这时，当一个线程访问`account`对象时，其他试图访问`account`对象的线程将会阻塞，直到该线程访问`account`对象结束。也就是说谁拿到那个锁谁就可以运行它所控制的那段代码。
&emsp;&emsp;当有一个明确的对象作为锁时，就可以用类似下面这样的方式写程序：

``` java
public void method3(SomeObject obj) {
    /* obj是锁定的对象 */
    synchronized (obj) {
        /* todo */
    }
}
```

当没有明确的对象作为锁，只是想让一段代码同步时，可以创建一个特殊的对象来充当锁：

``` java
class Test implements Runnable {
    private byte[] lock = new byte[0];  /* 特殊的instance变量 */
​
    public void method() {
        synchronized (lock) {
            /* 同步代码块 */
        }
    }
​
    public void run() {
    }
}
```

零长度的`byte`数组对象创建起来将比任何对象都经济：查看编译后的字节码，生成零长度的`byte`对象只需`3`条操作码，而`Object lock = new Object()`则需要`7`行操作码。

### 修饰一个方法

&emsp;&emsp;`Synchronized`修饰一个方法很简单，就是在方法的前面加`synchronized`：

``` java
public synchronized void method() {
    /* todo */
};
```

`synchronized`修饰方法和修饰一个代码块类似，只是作用范围不一样：修饰代码块是大括号括起来的范围，而修饰方法范围是整个函数。
&emsp;&emsp;`Synchronized`作用于整个方法有两个方法，写法一如下：

``` java
public synchronized void method() {
    /* todo */
}
```

写法二如下：

``` java
public void method() {
    synchronized (this) {
        /* todo */
    }
}
```

写法一修饰的是一个方法，写法二修饰的是一个代码块，但写法一与写法二是等价的，都是锁定了整个方法的内容。
&emsp;&emsp;在用`synchronized`修饰方法时，要注意以下几点：
&emsp;&emsp;1. `synchronized`关键字不能继承：虽然可以使用`synchronized`来定义方法，但`synchronized`并不属于方法定义的一部分，因此`synchronized`关键字不能被继承。如果在父类中的某个方法使用了`synchronized`关键字，而在子类中覆盖了这个方法，在子类中的这个方法默认情况下并不是同步的，而必须显式地在子类的这个方法中加上`synchronized`关键字才可以。当然，还可以在子类方法中调用父类中相应的方法，这样虽然子类中的方法不是同步的，但子类调用了父类的同步方法，因此，子类的方法也就相当于同步了。
&emsp;&emsp;这两种方式的例子代码如下，在子类方法中加上`synchronized`关键字：

``` java
class Parent {
    public synchronized void method() {
    }
}
​
class Child extends Parent {
    public synchronized void method() {
    }
}
```

在子类方法中调用父类的同步方法：

``` java
class Parent {
    public synchronized void method() {
    }
}
​
class Child extends Parent {
    public void method() {
        super.method();
    }
}
```

&emsp;&emsp;2. 在定义接口方法时不能使用`synchronized`关键字。
&emsp;&emsp;3. 构造方法不能使用`synchronized`关键字，但可以使用`synchronized`代码块来进行同步。

### 修饰一个静态的方法

&emsp;&emsp;`Synchronized`也可修饰一个静态方法，用法如下：

``` java
public synchronized static void method() {
    /* todo */
}
```

我们知道静态方法是属于类的而不属于对象的。同样的，`synchronized`修饰的静态方法锁定的是这个类的所有对象。

#### synchronized修饰静态方法

&emsp;&emsp;`SyncThread.java`如下：

``` java
class SyncThread implements Runnable {
    private static int count;
​
    public SyncThread() {
        count = 0;
    }
​
    public synchronized static void method() {
        for (int i = 0; i < 5; i++) {
            try {
                System.out.println(Thread.currentThread().getName() + " : " + (count++));
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
​
    public synchronized void run() {
        method();
    }
}
```

`test.java`如下：

``` java
public class test {
    public static void main(String[] args) {
        SyncThread syncThread1 = new SyncThread();
        SyncThread syncThread2 = new SyncThread();
        Thread thread1 = new Thread(syncThread1, "SyncThread1");
        Thread thread2 = new Thread(syncThread2, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
```

执行结果：

``` bash
SyncThread1 : 0
SyncThread1 : 1
SyncThread1 : 2
SyncThread1 : 3
SyncThread1 : 4
SyncThread2 : 5
SyncThread2 : 6
SyncThread2 : 7
SyncThread2 : 8
SyncThread2 : 9
```

`syncThread1`和`syncThread2`是`SyncThread`的两个对象，但在`thread1`和`thread2`并发执行时却保持了线程同步。这是因为`run`中调用了静态方法`method`，而静态方法是属于类的，所以`syncThread1`和`syncThread2`相当于用了同一把锁。

### 修饰一个类

&emsp;&emsp;`Synchronized`还可作用于一个类，用法如下：

``` java
class ClassName {
    public void method() {
        synchronized (ClassName.class) {
            /* todo */
        }
    }
}
```

`SyncThread.java`如下：

``` java
class SyncThread implements Runnable {
    private static int count;
​
    public SyncThread() {
        count = 0;
    }
​
    public static void method() {
        synchronized (SyncThread.class) {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public synchronized void run() {
        method();
    }
}
```

`synchronized`作用于一个类`T`时，是给这个类`T`加锁，`T`的所有对象用的是同一把锁。