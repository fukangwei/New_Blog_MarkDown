---
title: Scanner类
categories: Java
abbrlink: bc55b219
date: 2018-12-24 21:39:51
---
&emsp;&emsp;`java.util.Scanner`是`Java 5`的新特征，我们可以通过`Scanner`类来获取用户的输入。下面是创建`Scanner`对象的基本语法：<!--more-->

``` java
Scanner s = new Scanner(System.in);
```

接下来演示一个最简单的数据输入，并通过`Scanner`类的`next`与`nextLine`方法获取输入的字符串。在读取之前，一般需要使用`hasNext`与`hasNextLine`判断是否还有输入的数据：

``` java
import java.util.Scanner;
​
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        System.out.println("next方式接收：");

        if (scan.hasNext()) { /* 判断是否还有输入 */
            String str1 = scan.next();
            System.out.println("输入的数据为：" + str1);
        }

        scan.close();
    }
}
```

执行结果：

``` bash
next方式接收：
hello end
输入的数据为：hello
```

可以看到`end`字符串并未输出。接下来看`nextLine`：

``` java
import java.util.Scanner;
​
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        System.out.println("nextLine方式接收：");

        if (scan.hasNextLine()) { /* 判断是否还有输入 */
            String str2 = scan.nextLine();
            System.out.println("输入的数据为：" + str2);
        }

        scan.close();
    }
}
```

执行结果：

``` bash
nextLine方式接收：
hello end
输入的数据为：hello end
```

可以看到`end`字符串输出。

### next与nextLine区别

&emsp;&emsp;`next`的特性：

- 一定要读取到有效字符后才可以结束输入。
- 对输入有效字符之前遇到的空白，`next`方法会自动将其去掉。
- 只有输入有效字符后，才将其后面输入的空白作为分隔符或者结束符。
- `next`不能得到带有空格的字符串。

&emsp;&emsp;`nextLine`的特性：

- 以`Enter`为结束符，也就是说`nextLine`方法返回的是输入回车之前的所有字符。
- 可以获得空白。

如果要输入`int`或`float`类型的数据，在`Scanner`类中也有支持，但是在输入之前最好先使用`hasNextXxx`方法进行验证，再使用`nextXxx`来读取：

``` java
import java.util.Scanner;
​
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int i = 0;
        float f = 0.0f;
        System.out.print("输入整数：");

        if (scan.hasNextInt()) { /* 判断输入的是否是整数 */
            i = scan.nextInt(); /* 接收整数 */
            System.out.println("整数数据：" + i);
        } else { /* 如果输入错误 */
            System.out.println("输入的不是整数！");
        }

        System.out.print("输入小数：");

        if (scan.hasNextFloat()) { /* 判断输入的是否是小数 */
            f = scan.nextFloat(); /* 接收小数 */
            System.out.println("小数数据：" + f);
        } else { /* 如果输入错误 */
            System.out.println("输入的不是小数！");
        }

        scan.close();
    }
}
```

以下实例可以输入多个数字，并求其总和与平均数，每输入一个数字用回车确认，通过输入非数字来结束输入并输出执行结果：

``` java
import java.util.Scanner;
​
class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        System.out.println("请输入数据：");
        double sum = 0;
        int m = 0;
​
        while (scan.hasNextDouble()) {
            double x = scan.nextDouble();
            m = m + 1;
            sum = sum + x;
        }
​
        System.out.println(m + "个数的和为" + sum);
        System.out.println(m + "个数的平均值是" + (sum / m));
        scan.close();
    }
}
```

执行结果：

``` bash
请输入数据：
1.0
2.0
3.0
4.0
5.0
a
5个数的和为15.0
5个数的平均值是3.0
```