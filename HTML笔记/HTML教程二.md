---
title: HTML教程二
date: 2019-07-20 20:19:17
tags:
---
### HTML列表

&emsp;&emsp;下例演示无序列表：

<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h4>无序列表:</h4>
        <ul>
        <li>Coffee</li>
        <li>Tea</li>
        <li>Milk</li>
        </ul>
    </body>
</html>

    下例演示有序列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <ol>
        <li>Coffee</li>
        <li>Tea</li>
        <li>Milk</li>
        </ol>
        <ol start="50">
        <li>Coffee</li>
        <li>Tea</li>
        <li>Milk</li>
        </ol>
    </body>
</html>

    无序列表是一个项目的列表，此列项目使用粗体圆点(典型的小黑圆圈)进行标记。无序列表使用<ul>标签。同样，有序列表也是一列项目，列表项目使用数字进行标记。有序列表始于<ol>标签，每个列表项始于<li>标签，列表项项使用数字来标记。
    自定义列表不仅仅是一列项目，而是项目及其注释的组合。自定义列表以<dl>标签开始。每个自定义列表项以<dt>开始。每个自定义列表项的定义以<dd>开始。
<dl>
<dt>Coffee</dt>
<dd>- black hot drink</dd>
<dt>Milk</dt>
<dd>- white cold drink</dd>
</dl>
浏览器显示如下：
Coffee
- black hot drink
Milk
- white cold drink
列表项内部可以使用段落、换行符、图片、链接以及其他列表等等。
    下例演示不同类型的有序列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h4>编号列表：</h4>
        <ol>
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ol>
        <h4>大写字母列表：</h4>
        <ol type="A">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ol>
        <h4>小写字母列表：</h4>
        <ol type="a">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ol>
        <h4>罗马数字列表：</h4>
        <ol type="I">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ol>
        <h4>小写罗马数字列表：</h4>
        <ol type="i">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ol>
    </body>
</html>

    下例演示不同类型的无序列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p><b>注意：</b> 在 HTML 4中 ul 属性已废弃，HTML5 已不支持该属性，因此我们使用 CSS 代替来定义不同类型的无序列表如下：</p>
        <h4>圆点列表：</h4>
        <ul style="list-style-type:disc">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ul>
        <h4>圆圈列表：</h4>
        <ul style="list-style-type:circle">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ul>
        <h4>正方形列表：</h4>
        <ul style="list-style-type:square">
        <li>Apples</li>
        <li>Bananas</li>
        <li>Lemons</li>
        <li>Oranges</li>
        </ul>
    </body>
</html>

    下例演示如何嵌套列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h4>嵌套列表：</h4>
        <ul>
        <li>Coffee</li>
        <li>Tea
        <ul>
        <li>Black tea</li>
        <li>Green tea</li>
        </ul>
        </li>
        <li>Milk</li>
        </ul>
    </body>
</html>

    下例演示更复杂的嵌套列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>

    <body>
        <h4>嵌套列表：</h4>
        <ul>
        <li>Coffee</li>
        <li>Tea
        <ul>
        <li>Black tea</li>
        <li>Green tea
        <ul>
        <li>China</li>
        <li>Africa</li>
        </ul>
        </li>
        </ul>
        </li>
        <li>Milk</li>
        </ul>
    </body>
</html>

    下例演示一个定义列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h4>一个自定义列表：</h4>
        <dl>
        <dt>Coffee</dt>
        <dd>- black hot drink</dd>
        <dt>Milk</dt>
        <dd>- white cold drink</dd>
        </dl>
    </body>
</html>

HTML的区块
    HTML可以通过<div>和<span>将元素组合起来。大多数HTML元素被定义为块级元素或内联元素。块级元素在浏览器显示时，通常会以新行来开始(和结束)。实例为<h1>、<p>、<ul>和<table>。
    HTML的内联元素在显示时通常不会以新行开始，实例为<b>、<td>、<a>和<img>。
    HTML的<div>元素是块级元素，它可用于组合其他HTML元素的容器。<div>元素没有特定的含义。除此之外，由于它属于块级元素，浏览器会在其前后显示折行。如果与CSS一同使用，<div>元素可用于对大的内容块设置样式属性。<div>元素的另一个常见的用途是文档布局，它取代了使用表格定义布局的老式方法。使用<table>元素进行文档布局不是表格的正确用法。<table>元素的作用是显示表格化的数据。
    HTML的<span>元素是内联元素，可用作文本的容器，它也没有特定的含义。当与CSS一同使用时，<span>元素可用于为部分文本设置样式属性。
    HTML分组标签如下所示：
标签      描述
--------------
<div>     定义了文档的区域，块级(block-level)
<span>    用来组合文档中的行内元素，内联元素(inline)

HTML布局
    使用<div>元素的网页布局如下所示：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <div id="container" style="width:500px">
        <div id="header" style="background-color:#FFA500;">
        <h1 style="margin-bottom:0;">主要的网页标题</h1></div>
        <div id="menu" style="background-color:#FFD700;height:200px;width:100px;float:left;">
        <b>菜单</b><br>
        HTML<br>
        CSS<br>
        JavaScript</div>
        <div id="content" style="background-color:#EEEEEE;height:200px;width:400px;float:left;">
        内容在这里</div>
        <div id="footer" style="background-color:#FFA500;clear:both;text-align:center;">
        版权 © runoob.com</div>
        </div>
    </body>
</html>

    使用<table>元素的网页布局如下所示：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <table width="500" border="0">
        <tr>
        <td colspan="2" style="background-color:#FFA500;">
        <h1>主要的网页标题</h1>
        </td>
        </tr>
        <tr>
        <td style="background-color:#FFD700;width:100px;vertical-align:top;">
        <b>菜单</b><br>
        HTML<br>
        CSS<br>
        JavaScript
        </td>
        <td style="background-color:#eeeeee;height:200px;width:400px;vertical-align:top;">
        内容在这里</td>
        </tr>
        <tr>
        <td colspan="2" style="background-color:#FFA500;text-align:center;">
        版权 © runoob.com</td>
        </tr>
        </table>
    </body>
</html>

    大多数网站会把内容安排到多个列中(就像杂志或报纸那样)，可以使用<div>或者<table>元素来创建多列。CSS用于对元素进行定位，或者为页面创建背景以及色彩丰富的外观。虽然我们可以使用HTML的table标签来设计出漂亮的布局，但是table标签是不建议作为布局工具使用的，注意表格不是布局工具。
    使用CSS最大的好处是，如果把CSS代码存放到外部样式表中，那么站点会更易于维护。通过编辑单一的文件，就可以改变所有页面的布局。由于创建高级的布局非常耗时，使用模板是一个快速的选项。通过搜索引擎可以找到很多免费的网站模板，您可以使用这些预先构建好的网站布局，并优化它们。

HTML表单
    HTML表单用于搜集不同类型的用户输入。下例演示如何在HTML页面创建文本域，用户可以在文本域中写入文本。
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="">
        First name: <input type="text" name="firstname"><br>
        Last name: <input type="text" name="lastname">
        </form>
        <p><b>注意：</b> 表单本身是不可见的。并且注意一个文本字段的默认宽度是20个字符。</p>
    </body>
</html>

    下例演示如何创建HTML的密码域：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>

    <body>
        <form action="">
        Username: <input type="text" name="user"><br>
        Password: <input type="password" name="password">
        </form>
        <p><b>注意：</b> 密码字段中的字符是隐藏的(显示为星号或圆圈)。</p>
    </body>
</html>

    HTML的表单是一个包含表单元素的区域。表单元素是允许用户在表单中输入内容，比如文本域(textarea)、下拉列表、单选框(radio-buttons)、复选框(checkboxes)等等。表单使用表单标签<form>来设置，如下所示：
<form>
.
input 元素
.
</form>
    多数情况下被用到的表单标签是输入标签<input>，输入类型是由类型属性(type)定义的。大多数经常被用到的输入类型如下：
    1、文本域(Text Fields)
    文本域通过<input type="text">标签来设定，当用户要在表单中键入字母、数字等内容时，就会用到文本域，如下所示：
<form>
First name: <input type="text" name="firstname"><br>
Last name: <input type="text" name="lastname">
</form>

注意，表单本身并不可见。同时，在大多数浏览器中，文本域的缺省宽度是20个字符。
    2、密码字段
    密码字段通过标签<input type="password">来定义，如下所示：
<form>
Password: <input type="password" name="pwd">
</form>
浏览器显示效果如下：

注意，密码字段字符不会明文显示，而是以星号或圆点替代。
    3、单选按钮(Radio Buttons)
    <input type="radio">标签定义了表单单选框选项，如下所示：
<form>
<input type="radio" name="sex" value="male">Male<br>
<input type="radio" name="sex" value="female">Female
</form>

    4、复选框(Checkboxes)
    <input type="checkbox">定义了复选框，用户需要从若干给定的选择中选取一个或若干选项。
<form>
<input type="checkbox" name="vehicle" value="Bike">I have a bike<br>
<input type="checkbox" name="vehicle" value="Car">I have a car
</form>

    5、提交按钮(Submit Button)
    <input type="submit">定义了提交按钮，当用户单击确认按钮时，表单的内容会被传送到另一个文件。表单的动作属性定义了目的文件的文件名。由动作属性定义的这个文件通常会对接收到的输入数据进行相关的处理。
<form name="input" action="html_form_action.php" method="get">
Username: <input type="text" name="user">
<input type="submit" value="Submit">
</form>

假如您在上面的文本框内键入几个字母，然后点击确认按钮，那么输入数据会传送到“html_form_action.php”的页面，该页面将显示出输入的结果。

    下例演示如何在HTML中创建单选按钮：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="">
        <input type="radio" name="sex" value="male">Male<br>
        <input type="radio" name="sex" value="female">Female
        </form>
        <p><b>注意：</b>当用户点击一个单选按钮时，它就会被选中，其他同名的单选按钮就不会被选中。</p>
    </body>
</html>

    下例演示如何在HTML页面中创建简单的下拉列表框，下拉列表框是一个可选列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="">
        <select name="cars">
        <option value="volvo">Volvo</option>
        <option value="saab">Saab</option>
        <option value="fiat">Fiat</option>
        <option value="audi">Audi</option>
        </select>
        </form>
    </body>
</html>

    下例演示如何创建一个简单的带有预选值的下拉列表：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="">
        <select name="cars">
        <option value="volvo">Volvo</option>
        <option value="saab">Saab</option>
        <option value="fiat" selected>Fiat</option>
        <option value="audi">Audi</option>
        </select>
        </form>
    </body>
</html>

    下例演示如何创建文本域(多行文本输入控件)，用户可在文本域中写入文本，可写入字符的字数不受限制：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <textarea rows="10" cols="30">
        我是一个文本框。
        </textarea>
    </body>
</html>

    下例演示如何创建按钮，你可以对按钮上的文字进行自定义：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="">
        <input type="button" value="Hello world!">
        </form>
    </body>
</html>

    下例演示如何在数据周围绘制一个带标题的框：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="">
        <fieldset>
        <legend>Personal information:</legend>
        Name: <input type="text" size="30"><br>
        E-mail: <input type="text" size="30"><br>
        Date of birth: <input type="text" size="10">
        </fieldset>
        </form>
    </body>
</html>

    下例演示如何向页面添加表单，此表单包含两个输入框和一个确认按钮：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="demo-form.php">
        First name: <input type="text" name="FirstName" value="Mickey"><br>
        Last name: <input type="text" name="LastName" value="Mouse"><br>
        <input type="submit" value="提交">
        </form>
        <p>点击"提交"按钮，表单数据将被发送到服务器上的“demo-form.php”。</p>
    </body>
</html>

    下例表单包含两个复选框和一个确认按钮：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="demo-form.php" method="get">
        <input type="checkbox" name="vehicle" value="Bike"> I have a bike<br>
        <input type="checkbox" name="vehicle" value="Car" checked="checked"> I have a car<br>
        <input type="submit" value="提交">
        </form>
    </body>
</html>

    下例表单包含两个单选框和一个确认按钮：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <form action="demo-form.php" method="get">
        <input type="radio" name="sex" value="Male"> Male<br>
        <input type="radio" name="sex" value="Female" checked="checked"> Female<br>
        <input type="submit" value="提交">
        </form>
    </body>
</html>

    下例演示如何从表单发送电子邮件：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h3>发送邮件到 someone@example.com:</h3>
        <form action="MAILTO:someone@example.com" method="post" enctype="text/plain">
        Name:<br>
        <input type="text" name="name" value="your name"><br>
        E-mail:<br>
        <input type="text" name="mail" value="your email"><br>
        Comment:<br>
        <input type="text" name="comment" value="your comment" size="50"><br><br>
        <input type="submit" value="发送">
        <input type="reset" value="重置">
        </form>
    </body>
</html>

    HTML表单标签如下所示：
标签          描述
------------------
<form>        定义供用户输入的表单
<input>       定义输入域
<textarea>    定义文本域(一个多行的输入控件)
<label>       定义了<input>元素的标签，一般为输入标题
<fieldset>    定义了一组相关的表单元素，并使用外框包含起来
<legend>      定义了<fieldset>元素的标题
<select>      定义了下拉选项列表
<optgroup>    定义选项组
<option>      定义下拉列表中的选项
<button>      定义一个点击按钮
<datalist>    指定一个预先定义的输入控件选项列表
<keygen>      定义了表单的密钥对生成器字段
<output>      定义一个计算结果

HTML框架
    通过使用框架，你可以在同一个浏览器窗口中显示不止一个页面，即在当前页面中再显示一个页面。iframe语法如下所示：
<iframe src="URL"></iframe>
该URL指向不同的网页。
    iframe的height和width属性用来定义iframe标签的高度与宽度，属性默认以像素为单位，但是你可以指定其按比例显示(例如“80%”)。
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <iframe src="demo_iframe.htm" width="200" height="200"></iframe>
        <p>一些旧的浏览器不支持 iframe。</p>
        <p>如果浏览器不支持 iframes 则不会显示。</p>
    </body>
</html>

    iframe的frameborder属性用于定义iframe表示是否显示边框，设置属性值为“0”则移除iframe的边框：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <iframe src="demo_iframe.htm" width="200" height="200" frameborder="0">
        <p>您的浏览器不支持 iframe 标签。</p>
        </iframe>
    </body>
</html>

    iframe可以显示一个目标链接的页面，目标链接的属性必须使用iframe的属性，如下实例：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <iframe src="demo_iframe.htm" name="iframe_a"></iframe>
        <p><a href="http://www.runoob.com" target="iframe_a">RUNOOB.COM</a></p>
        <p><b>注意：</b> 因为 a 标签的 target 属性是名为 iframe_a 的 iframe 框架，所以在点击链接时页面会显示在 iframe框架中。</p>
    </body>
</html>


HTML颜色
    HTML颜色由红色、绿色、蓝色混合而成。HTML颜色由一个十六进制符号来定义，这个符号由红色、绿色和蓝色的值组成(RGB)。三种颜色的最小值是0(十六进制为#00)，最大值是255(十六进制为#FF)。这个表格给出了由三种颜色混合而成的具体效果：

    实例如下所示：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <p style="background-color:#FFFF00">
        通过十六进制设置背景颜色
        </p>
        <p style="background-color:rgb(255,255,0)">
        通过 rbg 值设置背景颜色
        </p>
        <p style="background-color:yellow">
        通过颜色名设置背景颜色
        </p>
    </body>
</html>

    目前所有浏览器都支持以下颜色名。141个颜色名称是在HTML和CSS颜色规范定义的(17标准颜色，再加124)。17标准颜色为黑色、蓝色、水、紫红色、灰色、绿色、石灰、栗色、海军、橄榄、橙、紫、红、白、银、蓝绿色、黄色。

HTML脚本
    JavaScript使HTML页面具有更强的动态和交互性。
    下例演示如何将脚本插入HTML文档：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <script>
        document.write("Hello World!")
        </script>
    </body>
</html>
    使用<noscript>标签来应对不支持脚本或禁用脚本的浏览器：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <script>
        document.write("Hello World!")
        </script>
        <noscript>抱歉，你的浏览器不支持 JavaScript!</noscript>
        <p>不支持 JavaScript 的浏览器会使用 &lt;noscript&gt; 元素中定义的内容（文本）来替代。</p>
    </body>
</html>
    HTML的<script>标签用于定义客户端脚本，比如JavaScript。<script>元素既可包含脚本语句，也可通过src属性指向外部脚本文件。JavaScript最常用于图片操作、表单验证以及内容动态更新。
    HTML的<noscript>标签提供无法使用脚本时的替代内容，比方在浏览器禁用脚本时，或浏览器不支持客户端脚本时。<noscript>元素可包含普通HTML页面的body元素中能够找到的所有元素。只有在浏览器不支持脚本或者禁用脚本时，才会显示<noscript>元素中的内容：
    JavaScript事件响应如下所示：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h1>我的第一个 JavaScript </h1>
        <p id="demo">
        JavaScript 可以触发事件，就像按钮点击。</p>
        <script>
        function myFunction()
        {
            document.getElementById("demo").innerHTML="Hello JavaScript!";
        }
        </script>
        <button type="button" onclick="myFunction()">点我</button>
    </body>
</html>

    JavaScript处理HTML样式如下所示：
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <h1>我的第一段 JavaScript</h1>
        <p id="demo">
        JavaScript 能改变 HTML 元素的样式。
        </p>
        <script>
        function myFunction()
        {
            x=document.getElementById("demo") // 找到元素
            x.style.color="#ff0000";          // 改变样式
        }
        </script>
        <button type="button" onclick="myFunction()">点击这里</button>
    </body>
</html>


HTML字符实体
    HTML中的预留字符必须被替换为字符实体。一些在键盘上找不到的字符也可以使用字符实体来替换。
    在HTML中不能使用小于号(<)和大于号(>)，这是因为浏览器会误认为它们是标签。如果希望正确地显示预留字符，我们必须在HTML源代码中使用字符实体(character entities)。字符实体类似这样：
&entity_name;
或
&#entity_number;
如需显示小于号，我们必须这样写“&lt;”、“&#60;”或“&#060;”。使用实体名而不是数字的好处是，名称易于记忆。不过坏处是，浏览器也许并不支持所有实体名称(对实体数字的支持却很好)。
    HTML中的常用字符实体是不间断空格“&nbsp;”。浏览器总是会截短HTML页面中的空格。如果您在文本中写10个空格，在显示该页面之前，浏览器会删除它们中的9个。如需在页面中增加空格的数量，您需要使用“&nbsp;”字符实体。
    发音符号是加到字母上的一个“glyph(字形)”。一些变音符号，如 尖音符“ ̀”和抑音符“ ́”。变音符号可以出现字母的上面和下面，或者字母里面，或者两个字母间。变音符号可以与字母、数字字符的组合来使用，以下是一些实例：
音标符    字符    Construct    输出结果
---------------------------------------
  ̀        a       a&#768;      à
  ́        a       a&#769;      á
  ^       a       a&#770;      â
  ̃        a       a&#771;      ã
  ̀        O       O&#768;      Ò
  ́        O       O&#769;      Ó
  ^       O       O&#770;      Ô
  ̃        O       O&#771;      Õ
    HTML字符实体名称对大小写敏感，如下所示：
显示结果    描述           实体名称    实体编号
-----------------------------------------------
            空格           &nbsp;      &#160;
<           小于号         &lt;        &#60;
>           大于号         &gt;        &#62;
&           和号           &amp;       &#38;
"           引号           &quot;      &#34;
'           撇号           &apos;      &#39;
￠          分             &cent;      &#162;
£           镑             &pound;     &#163;
¥           人民币/日元    &yen;       &#165;
€          欧元           &euro;      &#8364;
§          小节           &sect;      &#167;
©           版权           &copy;      &#169;
®           注册商标       &reg;       &#174;
™           商标           &trade;     &#8482;
×          乘号           &times;     &#215;
÷          除号           &divide;    &#247;

HTML的URL
    URL是一个网页地址，可以由字母组成，如“runoob.com”，或互联网协议(IP)地址，如“192.68.20.50”。大多数人进入网站使用网站域名来访问，因为名字比数字更容易记住。
    Web浏览器通过URL从Web服务器请求页面，当您点击HTML页面中的某个链接时，对应的<a>标签指向万维网上的一个地址。
    URL只能使用ASCII字符集，由于URL常常会包含ASCII集合之外的字符，URL必须转换为有效的ASCII格式。URL编码使用“%”其后跟随两位的十六进制数来替换非ASCII字符。URL不能包含空格，URL编码通常使用“+”来替换空格。
    URL编码实例如下所示：
字符    URL编码
---------------
€      %80
£       %A3
©       %A9
®       %AE
À       %C0
Á       %C1
Â       %C2
Ã       %C3
Ä       %C4
Å       %C5

HTML之XHTML
    XHTML是以XML格式编写的HTML。XHTML与HTML4.01几乎是相同的，它是更严格、更纯净的HTML版本，是以XML应用的方式定义的HTML，得到所有主流浏览器的支持。
    为什么使用XHTML？因特网上的很多页面包含了“糟糕”的HTML，如果在浏览器中查看，下面的HTML代码运行起来非常正常(即使它并未遵守HTML规则)：
<html>
    <head>
        <meta charset="utf-8">
        <title>这是一个不规范的 HTML</title>
    <body>
        <h1>不规范的 HTML
        <p>这是一个段落
    </body>
    XML是一种必须正确标记且格式良好的标记语言。今日的科技界存在一些不同的浏览器技术，其中一些在计算机上运行，而另一些可能在移动电话或其他小型设备上运行。小型设备往往缺乏解释“糟糕”的标记语言的资源和能力。所以通过结合XML和HTML的长处，开发出了XHTML。
    XHTML是作为XML被重新设计的HTML，与HTML相比最重要的区别：
    1、文档结构
XHTML的DOCTYPE是强制性的。
<html>中的“XML namespace”属性是强制性的。
<html>、<head>、<title>以及<body>也是强制性的。
    2、元素语法
    XHTML元素必须正确嵌套，必须始终关闭，必须小写，XHTML文档必须有一个根元素。
    3、属性语法
    XHTML属性必须使用小写，属性值必须用引号包围，属性最小化也是禁止的。

    <!DOCTYPE ....>是强制性的。XHTML 文档必须进行XHTML文档类型声明(XHTML DOCTYPE declaration)。<html>、<head>、<title>和<body>元素也必须存在，并且必须使用<html>中的xmlns属性为文档规定xml命名空间。下面的例子展示了带有最少的必需标签的XHTML文档：
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8">
        <title>文档标题</title>
    </head>
    <body>
        文档内容
    </body>
</html>
    XHTML元素必须合理嵌套。在HTML中，一些元素可以不互相嵌套，像这样：
<b><i>粗体和斜体文本</b></i>
在XHTML中，所有的元素都必须互相合理地嵌套，像这样：
<b><i>粗体和斜体文本</i></b>
    XHTML元素必须有关闭标签，错误示例：
<p>这是一个段落
<p>这是另外一个段落
正确示例：
<p>这是一个段落</p>
<p>这是另外一个段落</p>
    空元素必须包含关闭标签，错误示例：
分行：<br>
水平线：<hr>
图片：<img src="happy.gif" alt="Happy face">
正确示例：
分行：<br />
水平线：<hr />
图片：<img src="happy.gif" alt="Happy face" />
    XHTML元素必须是小写，错误示例：
<BODY>
<P>这是一个段落</P>
</BODY>
正确示例：
<body>
<p>这是一个段落</p>
</body>
    属性名称必须是小写，错误示例：
<table WIDTH="100%">
正确示例：
<table width="100%">
    属性值必须有引号，错误示例：
<table width=100%>
正确示例：
<table width="100%">
    不允许属性简写，错误示例：
<input checked>
<input readonly>
<input disabled>
<option selected>
正确示例：
<input checked="checked">
<input readonly="readonly">
<input disabled="disabled">
<option selected="selected">

    如何将HTML转换为XHTML？添加一个XHTML的<!DOCTYPE>到你的网页中，将xmlns属性添加到每个页面的html元素中，改变所有的元素为小写，关闭所有的空元素，修改所有的属性名称为小写，所有属性值添加引号。