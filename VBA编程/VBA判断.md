---
title: VBA判断
categories: VBA编程
date: 2019-07-21 11:19:06
---
### if语句

&emsp;&emsp;`if`语句如下：<!--more-->

``` vbscript
If boolean_expression Then
    ' User code
End If
```

### if-else语句

&emsp;&emsp;`if-else`语句如下：

``` vbscript
If boolean_expression Then
    ' User code
Else
    ' User code
End If
```

### if-elseif-else语句

&emsp;&emsp;`if-elseif-else`语句如下：

``` vbscript
If boolean_expression Then
    ' User code
ElseIf boolean_expression Then
    ' User code
Else
    ' User code
End If
```

### 嵌套if语句

&emsp;&emsp;嵌套`if`语句如下：

``` vbscript
If boolean_expression Then
    ' User code
    If boolean_expression Then
        ' User code
    ElseIf boolean_expression Then
        ' User code
    Else
        ' User code
    End If
Else
    ' User code
End If
```

### switch语句

&emsp;&emsp;`switch`语句如下：

``` vbscript
Select Case expression
    Case expression_list_1
        ' User code
    Case expression_list_2
        ' User code
    Case expression_list_n
        ' User code
    Case Else
        ' User code
End Select
```

示例如下：

``` vbscript
Private Sub switch_demo_Click()
   Dim MyVar As Integer
   MyVar = 1

   Select Case MyVar
        Case 1
            MsgBox "The Number is the Least Composite Number"
        Case 2
            MsgBox "The Number is the only Even Prime Number"
        Case 3
            MsgBox "The Number is the Least Odd Prime Number"
        Case Else
            MsgBox "Unknown Number"
   End Select
End Sub
```