---
title: VBA字符串
date: 2019-07-21 12:53:52
categories: VBA编程
---
&emsp;&emsp;预定义的`VBA`字符串函数可以帮助开发人员非常有效地处理字符串，以下是`VBA`支持的字符串处理方法。
<!--more-->
### Instr函数

&emsp;&emsp;`InStr`函数返回一个字符串第一次出现的位置，从左到右搜索：

``` vbscript
InStr([start,] string1, string2 [, compare])
```

- `start`：可选参数，指定搜索的起始位置。
- `string1`：必需的参数，要搜索的字符串。
- `string2`：必需的参数，要在`string1`中搜索的字符串。
- `compare`：可选参数，指定要使用的字符串比较方式，有以下选项：

1. `vbBinaryCompare`：执行二进制比较(默认)。
2. `vbTextCompare`：执行文本比较。

``` vbscript
Private Sub Constant_demo_Click()
    Dim Var As Variant
    Var = "Microsoft VBScript"
    MsgBox("Line 1: " & InStr(1, Var, "s"))
    MsgBox("Line 2: " & InStr(7, Var, "s"))
    MsgBox("Line 3: " & InStr(1, Var, "f", 1))
    MsgBox("Line 4: " & InStr(1, Var, "t", 0))
    MsgBox("Line 5: " & InStr(1, Var, "i"))
    MsgBox("Line 6: " & InStr(7, Var, "i"))
    MsgBox("Line 7: " & InStr(Var, "VB"))
End Sub
```

运行结果：

``` vbscript
Line 1: 6
Line 2: 0
Line 3: 8
Line 4: 9
Line 5: 2
Line 6: 16
Line 7: 11
```

### InStrRev函数

&emsp;&emsp;`InStrRev`函数返回一个字符串在另一个字符串中的最后一次出现的位置：

``` vbscript
InStrRev(string1, string2 [, start] [, compare])
```

- `string1`：必需的参数，要搜索的字符串。
- `string2`：必需的参数，要在`string1`中搜索的字符串。
- `start`：可选参数，指定搜索的起始位置。
- `compare`：可选参数，指定要使用的字符串比较方式，有以下选项：

1. `vbBinaryCompare`：执行二进制比较(默认)。
2. `vbTextCompare`：执行文本比较。

``` vbscript
Private Sub Constant_demo_Click()
    var = "Microsoft VBScript"
    MsgBox("Line 1: " & InStrRev(var, "s", 10))
    MsgBox("Line 2: " & InStrRev(var, "s", 7))
    MsgBox("Line 3: " & InStrRev(var, "f", -1, vbTextCompare))
    MsgBox("Line 4: " & InStrRev(var, "t", 5))
    MsgBox("Line 5: " & InStrRev(var, "i", 7))
End Sub
```

运行结果：

``` vbscript
Line 1: 6
Line 2: 6
Line 3: 8
Line 4: 0
Line 5: 2
```

### Lcase函数

&emsp;&emsp;`LCase`函数将输入的字符串转换为小写字母：

``` vbscript
Lcase(String)
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    var = "Microsoft VBScript"
    MsgBox("Line 1: " & LCase(var))

    var = "MS VBSCRIPT"
    MsgBox("Line 2: " & LCase(var))

    var = "microsoft"
    MsgBox("Line 3: " & LCase(var))
End Sub
```

运行结果：

``` vbscript
Line 1: microsoft vbscript
Line 2: ms vbscript
Line 3: microsoft
```

### UCase函数

&emsp;&emsp;`UCase`函数将输入的字符串转换为大写字母：

``` vbscript
UCase(String)
```

### Left函数

&emsp;&emsp;`Left`函数从给定输入字符串的左侧返回指定数量的字符：

``` vbscript
Left(String, Length)
```

- `String`：必需的参数，原始字符串。
- `Length`：必需的参数，指定要返回的字符数。

``` vbscript
Private Sub Constant_demo_Click()
    Dim var as Variant

    var = "Microsoft VBScript"
    MsgBox("Line 1: " & Left(var, 2))

    var = "MS VBSCRIPT"
    MsgBox("Line 2: " & Left(var, 5))

    var = "microsoft"
    MsgBox("Line 3: " & Left(var, 9))
End Sub
```

### Mid函数

&emsp;&emsp;`Mid`函数返回给定输入字符串中指定数量的字符：

``` vbscript
Mid(String, start [, Length])
```

- `String`：必需的参数，原始字符串。
- `Start`：必需的参数，指定起始位置。
- `Length`：必需的参数，指定要返回的字符数。

``` vbscript
Private Sub Constant_demo_Click()
   Dim var as Variant
   var = "Microsoft VBScript"
   MsgBox("Line 1: " & Mid(var, 2))
   MsgBox("Line 2: " & Mid(var, 2, 5))
   MsgBox("Line 3: " & Mid(var, 5, 7))
End Sub
```

运行结果：

``` vbscript
Line 1: icrosoft VBScript
Line 2: icros
Line 3: osoft V
```

### Ltrim函数

&emsp;&emsp;`Ltrim`函数删除字符串左侧的空格：

``` vbscript
LTrim(String)
```

### Rtrim函数

&emsp;&emsp;`Rtrim`函数删除字符串右侧的空格：

``` vbscript
RTrim(String)
```

### Trim函数

&emsp;&emsp;`Trim`函数删除给定输入字符串的前导空格和尾随空格：

``` vbscript
Trim(String)
```

### Len函数

&emsp;&emsp;`Len`函数返回给定输入字符串的长度(包括空格)：

``` vbscript
Len(String)
```

### Space函数

&emsp;&emsp;用特定数量的空格填充字符串：

``` vbscript
Space(number)
```

- `number`：必需的参数，想要添加到给定字符串的空格数量。

``` vbscript
Private Sub Constant_demo_Click()
    Dim var1 as Variant

    var1 = "Microsoft"
    Dim var2 as Variant

    var2 = "VBScript"
    MsgBox(var1 & Space(2) & var2)
End Sub
```

运行结果：

``` vbscript
Microsoft  VBScript
```

### StrComp函数

&emsp;&emsp;`StrComp`函数用于比较两个给定字符串：

``` vbscript
StrComp(string1, string2 [, compare])
```

- `string1`：必需的参数，第一个字符串表达式。
- `string2`：必需的参数，第二个字符串表达式。
- `compare`：一个可选参数，指定要使用的字符串比较方式，有以下选项：

1. `vbBinaryCompare`：执行二进制比较(默认)。
2. `vbTextCompare`：执行文本比较。

`StrComp`函数返回一个整数值：

- 如果`String1 < String2`，那么`StrComp`函数返回`-1`。
- 如果`String1 = String2`，那么`StrComp`函数返回`0`。
- 如果`String1 > String2`，那么`StrComp`函数返回`1`。

### String函数

&emsp;&emsp;`String`函数使用指定的字符填充指定次数的字符串：

``` vbscript
String(number, character)
```

- `number`：必需的参数，字符重复的次数。
- `character`：必需的参数，需要重复的字符。

``` vbscript
Private Sub Constant_demo_Click()
    MsgBox("Line 1: " & String(3, "$"))
    MsgBox("Line 2: " & String(4, "*"))
    MsgBox("Line 3: " & String(5, 100))
    MsgBox("Line 4: " & String(6, "ABCDE"))
End Sub
```

运行结果：

``` vbscript
Line 1: $$$
Line 2: ****
Line 3: ddddd
Line 4: AAAAAA
```

### StrReverse函数

&emsp;&emsp;`StrReverse`函数反转指定的字符串：

``` vbscript
StrReverse(string)
```

### Replace函数

&emsp;&emsp;`Replace`函数使用另一个字符串替换字符串的指定部分，并且是指定的次数：

``` vbscript
Replace(string, find, replacewith [, start [, count [, compare]]])
```

- `string`：必需参数，被搜索的字符串。
- `find`：必需参数，将被替换的字符串部分。
- `replacewith`：必需参数，用于替换的子字符串。
- `start`：可选参数，指定的开始位置，默认值是`1`。起始位置之前的所有字符将被删除。
- `count`：可选参数，规定要执行的替换的次数。默认值是`-1`，表示进行所有可能的替换。
- `compare`：可选参数，规定要使用的字符串比较类型，有以下选项：

1. `vbBinaryCompare`：执行二进制比较(默认)。
2. `vbTextCompare`：执行文本比较。

``` vbscript
Private Sub Constant_demo_Click()
    txt = "This is a beautiful day!"
    ' 把单词“beautiful”替换为“fantastic”
    MsgBox("Line 1: " & Replace(txt, "beautiful", "fantastic"))
    ' 把字母“i”替换为“##”
    MsgBox("Line 2: " & Replace(txt, "i", "##"))
    ' 把字母“i”替换为“##”，从位置15开始。请注意，位置15之前的所有字符都会被删除
    MsgBox("Line 3: " & Replace(txt, "i", "##", 15))
    ' 从位置1开始，把前2个字母“i”替换为“##”
    MsgBox("Line 4: " & Replace(txt, "i", "##", 1, 2))
    ' 把字母“t”替换为“##”，采用文本和二进制比较
    MsgBox("Line 5: " & Replace(txt, "t", "##", 1, -1, vbTextCompare))
    MsgBox("Line 6: " & Replace(txt, "t", "##", 1, -1, vbBinaryCompare))
End Sub
```

运行结果：

``` vbscript
Line 1: This is a fantastic day!
Line 2: Th##s ##s a beaut##ful day!
Line 3: t##ful day!
Line 4: Th##s ##s a beautiful day!
Line 5: ##his is a beau##iful day!
Line 6: This is a beau##iful day!
```