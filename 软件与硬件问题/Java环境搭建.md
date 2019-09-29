---
title: Java环境搭建
categories: 软件与硬件问题
abbrlink: 413332c5
date: 2019-01-13 19:44:15
---
### Java官方JDK

&emsp;&emsp;在`系统变量`中设置`3`项属性：`JAVA_HOME`、`PATH`、`CLASSPATH`。

- `JAVA_HOME`的值是`D:\Program Files (x86)\Java\jdk1.8.0_91`，这个路径就是`JDK`安装的路径。
- `CLASSPATH`的值是`.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;`，记得前面有个`.`。
- 在`Path`后添加`%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;`。

&emsp;&emsp;配置完成之后，打开`cmd`，然后输入`java -version`进行测试。

### OpenJDK

&emsp;&emsp;从`http://jdk.java.net/10/`下载`openjdk`的`Windows`版本，然后解压至系统`C`盘。随后增加`JAVA_HOME`环境变量，其值为`C:\jdk-10.0.1`。然后在环境变量`path`中增加`%JAVA_HOME%\bin`，在终端输入`java -version`进行测试。