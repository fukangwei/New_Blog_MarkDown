---
title: URL处理
date: 2018-12-21 15:50:37
categories: Java
---
&emsp;&emsp;`URL`(`Uniform Resource Locator`)中文名为统一资源定位符，有时也被俗称为网页地址。它表示为互联网上的资源，例如网页或者`FTP`地址。`URL`可以分为如下几个部分：

``` bash
protocol://host:port/path?query#fragment
```

`protocol`可以是`HTTP`、`HTTPS`、`FTP`和`File`，`port`为端口号，`path`为文件路径及文件名。`HTTP`协议的`URL`实例如下：

``` bash
http://www.runoob.com/index.html?language=cn#j2se
```

协议(`protocol`)是`http`；主机(`host`)是`www.runoob.com`；端口号(`port`)是`80`，以上`URL`实例并未指定端口，因为`HTTP`协议默认的端口号为`80`；文件路径(`path`)是`/index.html`；请求参数(`query`)是`language=cn`；定位位置(`fragment`)是`j2se`，定位到网页中`id`属性为`j2se`的`HTML`元素位置。

### URL类方法

&emsp;&emsp;在`java.net`包中定义了`URL`类，该类用来处理有关`URL`的内容。`java.net.URL`提供了丰富的`URL`构建方式，并可以通过`java.net.URL`来获取资源：

- `public URL(String protocol, String host, int port, String file) throws MalformedURLException`：通过给定的参数(协议、主机名、端口号、文件名)创建`URL`。
- `public URL(String protocol, String host, String file) throws MalformedURLException`：使用指定的协议、主机名、文件名创建`URL`，端口使用协议的默认端口。
- `public URL(String url) throws MalformedURLException`：通过给定的`URL`字符串创建`URL`。
- `public URL(URL context, String url) throws MalformedURLException`：使用基地址和相对`URL`创建。

`URL`类中包含了很多方法用于访问`URL`的各个部分，具体方法及描述如下：

- `public String getPath()`：返回`URL`路径部分。
- `public String getQuery()`：返回`URL`查询部分。
- `public String getAuthority()`：获取此`URL`的授权部分。
- `public int getPort()`：返回`URL`端口部分
- `public int getDefaultPort()`：返回协议的默认端口号。
- `public String getProtocol()`：返回`URL`的协议。
- `public String getHost()`：返回`URL`的主机。
- `public String getFile()`：返回`URL`文件名部分
- `public String getRef()`：获取此`URL`的锚点(也称为`引用`)。
- `public URLConnection openConnection() throws IOException`：打开一个`URL`连接，并运行客户端访问资源。

&emsp;&emsp;下面的实例演示了使用`java.net`的`URL`类获取`URL`的各个部分参数：

``` java
import java.net.*;
import java.io.*;
​
public class URLDemo {
    public static void main(String[] args) {
        try {
            URL url = new URL("http://www.runoob.com/index.html?language=cn#j2se");
            System.out.println("URL为：" + url.toString());
            System.out.println("协议为：" + url.getProtocol());
            System.out.println("验证信息：" + url.getAuthority());
            System.out.println("文件名及请求参数：" + url.getFile());
            System.out.println("主机名：" + url.getHost());
            System.out.println("路径：" + url.getPath());
            System.out.println("端口：" + url.getPort());
            System.out.println("默认端口：" + url.getDefaultPort());
            System.out.println("请求参数：" + url.getQuery());
            System.out.println("定位位置：" + url.getRef());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

执行结果：

``` bash
URL为：http://www.runoob.com/index.html?language=cn#j2se
协议为：http
验证信息：www.runoob.com
文件名及请求参数：/index.html?language=cn
主机名：www.runoob.com
路径：/index.html
端口：-1
默认端口：80
请求参数：language=cn
定位位置：j2se
```

### URLConnections类方法

&emsp;&emsp;`openConnection`返回一个`java.net.URLConnection`。如果你连接`HTTP`协议的`URL`，`openConnection`方法返回`HttpURLConnection`对象；如果你连接的`URL`为一个`JAR`文件，`openConnection`方法将返回`JarURLConnection`对象。
&emsp;&emsp;`URLConnection`方法列表如下：

- `Object getContent()`：检索URL链接内容。
- `Object getContent(Class[] classes)`：检索URL链接内容。
- `String getContentEncoding()`：返回头部`content-encoding`字段值。
- `int getContentLength()`：返回头部`content-length`字段值。
- `String getContentType()`：返回头部`content-type`字段值。
- `int getLastModified()`：返回头部`last-modified`字段值。
- `long getExpiration()`：返回头部`expires`字段值。
- `long getIfModifiedSince()`：返回对象的`ifModifiedSince`字段值。
- `public void setDoInput(boolean input)`：URL连接可用于输入和/或输出。如果打算使用URL连接进行输入，则将DoInput标志设置为true；如果不打算使用，则设置为false。默认值为true。
- `public void setDoOutput(boolean output)`：URL连接可用于输入和/或输出。如果打算使用URL连接进行输出，则将DoOutput标志设置为true；如果不打算使用，则设置为false。默认值为false。
- `public InputStream getInputStream() throws IOException`：返回URL的输入流，用于读取资源。
- `public OutputStream getOutputStream() throws IOException`：返回URL的输出流，用于写入资源。
- `public URL getURL()`：返回URLConnection对象连接的URL。

&emsp;&emsp;以下实例中URL采用了HTTP协议：

``` java
import java.net.*;
import java.io.*;
​
public class URLConnDemo {
    public static void main(String[] args) {
        try {
            URL url = new URL("http://www.runoob.com");
            URLConnection urlConnection = url.openConnection();
            HttpURLConnection connection = null;

            if (urlConnection instanceof HttpURLConnection) {
                connection = (HttpURLConnection) urlConnection;
            } else {
                System.out.println("请输入URL地址");
                return;
            }

            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
            String urlString = "";
            String current;

            while ((current = in.readLine()) != null) {
                urlString += current;
            }

            System.out.println(urlString);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

执行结果是菜鸟教程首页`http://www.runoob.com`的HTML内容。