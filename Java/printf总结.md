---
title: printf总结
categories: Java
abbrlink: f9eccdae
date: 2018-12-26 12:05:28
---
&emsp;&emsp;代码如下：<!--more-->

``` java
import java.util.Date;
​
/* 使用“java.io.PrintStream”的printf方法实现C风格的输出。printf方法的
   第一个参数为输出的格式；第二个参数是可变长的，表示待输出的数据对象 */
public class Printf {
    public static void main(String[] args) {
        /* 输出字符串 */
        System.out.printf("%s", 1212); /* “%s”表示输出字符串，也就是将后面的字符串替换模式中的“%s” */
        System.out.printf("%s%n", "end line"); /* “%n”表示换行 */
        System.out.printf("%s = %s%n", "Name", "Zhangsan"); /* 还可以支持多个参数 */
        System.out.printf("%S = %s%n", "Name", "Zhangsan"); /* “%S”将字符串以大写形式输出 */
        /* 支持多个参数时，可以插入变量编号，“1$”表示第一个字符串，“3$”表示第3个字符串 */
        System.out.printf("%1$s = %3$s %2$s%n", "Name", "san", "Zhang");
​
        /* 输出boolean类型 */
        System.out.printf("true = %b; false = ", true);
        System.out.printf("%b%n", false);
​
        /* 输出整数类型 */
        Integer iObj = 342;
        System.out.printf("%d; %d; %d%n", -500, 2343L, iObj); /* “%d”表示将整数格式化为10进制整数 */
        System.out.printf("%o; %o; %o%n", -500, 2343L, iObj); /* “%o”表示将整数格式化为8进制整数 */
        System.out.printf("%x; %x; %x%n", -500, 2343L, iObj); /* “%x”表示将整数格式化为16进制整数 */
        System.out.printf("%X; %X; %X%n", -500, 2343L, iObj); /* “%X”表示将整数格式化为16进制整数，并且字母变成大写形式 */
​
        /* 输出浮点类型 */
        Double dObj = 45.6d;
        System.out.printf("%e; %e; %e%n", -756.403f, 7464.232641d, dObj); /* “%e”表示以科学技术法输出浮点数 */
        System.out.printf("%E; %E; %E%n", -756.403f, 7464.232641d, dObj); /* “%E”表示以科学技术法输出浮点数，并且为大写形式 */
        System.out.printf("%f; %f; %f%n", -756.403f, 7464.232641d, dObj); /* “%f”表示以十进制格式化输出浮点数 */
        System.out.printf("%.1f; %.3f; %f%n", -756.403f, 7464.232641d, dObj); /* 还可以限制小数点后的位数 */
​
        /* 输出日期类型 */
        /* “%t”表示格式化日期时间类型，“%T”是时间日期的大写形式，在“%t”之后用特定的字母表示不同的输出格式 */
        Date date = new Date();
        long dataL = date.getTime();
        /* “%t”之后用y表示输出日期的年份(2位数的年，如99)，“%t”之后用m表示输出日期的月份，“%t”之后用d表示输出日期的日号 */
        System.out.printf("%1$ty-%1$tm-%1$td; %2$ty-%2$tm-%2$td%n", date, dataL);
        /* “%t”之后用Y表示输出日期的年份(4位数的年)，“%t”之后用B表示输出日期的月份的完整名，“%t”之后用b表示输出日期的月份的简称 */
        System.out.printf("%1$tY-%1$tB-%1$td; %2$tY-%2$tb-%2$td%n", date, dataL);
        System.out.printf("%1$tD%n", date); /* “%t”之后用D表示以“%tm/%td/%ty”格式化日期 */
        System.out.printf("%1$tF%n", date); /* “%t”之后用F表示以“%tY-%tm-%td”格式化日期 */
​
        /* 输出时间类型 */
        /* “%t”之后用H表示输出时间的时(24进制)，“%t”之后用I表示输出时间的时(1          “%t”之后用M表示输出时间的分，“%t”之后用S表示输出时间的秒 */
        System.out.printf("%1$tH:%1$tM:%1$tS; %2$tI:%2$tM:%2$tS%n", date, dataL);
        System.out.printf("%1$tH:%1$tM:%1$tS %1$tL%n", date); /* “%t”之后用L表示输出时间的秒中的毫秒 */
        System.out.printf("%1$tH:%1$tM:%1$tS %1$tL %1$tp%n", date); /* “%t”之后用p表示输出时间的上午或下午信息 */
        System.out.printf("%1$tR%n", date); /* “%t”之后用R表示以“%tH:%tM”格式化时间 */
        System.out.printf("%1$tT%n", date); /* “%t”之后用T表示以“%tH:%tM:%tS”格式化时间 */
        System.out.printf("%1$tr%n", date); /* “%t”之后用r表示以“%tI:%tM:%tS %Tp”格式化时间 */
​
        /* 输出星期 */
        System.out.printf("%1$tF %1$tA%n", date); /* %t之后用A表示得到星期几的全称 */
        System.out.printf("%1$tF %1$ta%n", date); /* %t之后用a表示得到星期几的简称 */
        System.out.printf("%1$tc%n", date); /* 输出时间日期的完整信息 */
    }
}
```