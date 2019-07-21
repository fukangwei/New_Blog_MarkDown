&emsp;&emsp;变量是一个指定的内存位置，用于保存脚本执行过程中可以更改的值。以下是命名变量的基本规则。

- 变量名称必须使用一个字母作为第一个字符。
- 变量名称不能使用空格、句点`.`、感叹号`!`或字符`@`、`&`、`$`、`#`。
- 变量名称的长度不能超过`255`个字符。
- 不能使用`Visual Basic`保留关键字作为变量名称。

在`VBA`中，变量需要在使用它们之前声明：

``` vbscript
Dim variable_name As variable_type
```

数据类型有许多`VBA`数据类型，可以分为两大类，即数字和非数字数据类型。

### 数字数据类型

&emsp;&emsp;下表显示数字数据类型和允许的值范围：

数字类型    | 范围值
-----------|-------
`Byte`     | `0 ~ 255`
`Integer`  | `-32,768 ~ 32,767`
`Long`     | `-2,147,483,648 ~ 2,147,483,648`
`Single`   | 负值：`-3.402823E+38 ~ -1.401298E-45`；正值：`1.401298E-45 ~ 3.402823E+38`
`Double`   | 负值：`-1.79769313486232E+308 ~ -4.94065645841247E-324`，正值：`4.94065645841247E-324 ~ 1.79769313486232E+308`
`Currency` | `-922,337,203,685,477.5808 ~ 922,337,203,685,477.5807`
`Decimal`  | 如果不使用小数，则为`+/- 79,228,162,514,264,337,593,543,950,335`；如果使用小数，则为`+/- 7.9228162514264337593543950335`

### 非数字数据类型

&emsp;&emsp;下表显示了非数字数据类型和允许的值范围：

数字类型              | 范围值
---------------------|--------
固定长度`String`      | `1 ~ 65,400`个字符
可变长度`String`      | `0`到`20`亿个字符
`Date`               | `100`年`1`月`1`日至`9999`年`12`月`31`日
`Boolean`            | `True/False`
`Object`             | 任何嵌入的对象
`Variant`(`numeric`) | `16`个字节，任何数字值，最大可达`Double`的范围
`Variant`(`text`)    | `22`个字节加上字符串长度，与变长`String`有相同的范围

``` vbscript
Private Sub VariablesDemo()
   Dim password As String
   password = "123456"
   Dim num As Integer
   num = 1234
   Dim BirthDay As Date
   BirthDay = DateValue("1998-10-11")
   MsgBox ("设置的密码是：" & password & Chr(10) & "num的值是：" & num & Chr(10) & "Birthday的值是：" & BirthDay)
End Sub
```

---

&emsp;&emsp;常量是一个命名的内存位置，用于保存脚本执行期间固定的值。如果用户试图更改常量值，则脚本执行结束时会出现错误。常量声明与声明变量相同，其语法如下：

``` vbscript
Const constant_name As constant_type = constant_value
```

参考代码如下：

``` vbscript
Private Sub ConstantDemo()
Const MyInteger As Integer = 720
Const myDate As Date = #10/21/2000#
Const myDay As String = "Sunday"
MsgBox ("整数值是：" & MyInteger & Chr(10) & "myDate的值是：" & myDate & Chr(10) & "myDay的值是：" & myDay)
End Sub
```