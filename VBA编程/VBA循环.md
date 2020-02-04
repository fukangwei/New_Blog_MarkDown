---
title: VBA循环
categories: VBA编程
abbrlink: eae99fb3
date: 2019-07-21 12:41:49
---
### For循环

&emsp;&emsp;`For`循环的语法如下：<!--more-->

``` vbscript
For counter = start To end [Step stepcount]
    ' User code
    [Exit For] ' 如果需要终止循环，则使用该行代码
    ' User code
Next
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    Dim a As Integer
    a = 10

    For i = 0 To a Step 2 ' i is the counter variable and it is incremented by 2
        MsgBox ("The value is i is: " & i)
        If i = 4 Then
            i = i * 10 ' This is executed only if i=4
            MsgBox ("The value is i is: " & i)
            Exit For ' Exited when i=4
        End If
    Next
End Sub
```

### For-Each循环

&emsp;&emsp;`For-Each`循环的语法如下：

``` vbscript
For Each element In Group
    ' User code
    [Exit For] ' 如果需要终止循环，则使用该行代码
    ' User code
Next
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    ' fruits is an array
    fruits = Array("苹果", "橙子", "樱桃")
    Dim fruitnames As Variant

    ' iterating using For-each loop.
    For Each Item In fruits
        fruitnames = fruitnames & Item & Chr(10)
    Next

    MsgBox fruitnames
End Sub
```

### While-Wend循环

&emsp;&emsp;`While-Wend`循环的语法如下：

``` vbscript
While condition
    ' User code
Wend
````

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    Dim Counter : Counter = 10

    While Counter < 15 ' Test value of Counter
        Counter = Counter + 1 ' Increment Counter
        MsgBox "The Current Value of the Counter is: " & Counter
    Wend ' While loop exits if Counter Value becomes 15
End Sub
```

### Do-While循环

&emsp;&emsp;`Do-While`循环语法如下：

``` vbscript
Do While condition
    ' User code
    [Exit Do] ' 如果需要终止循环，则使用该行代码
    ' User code
Loop
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    Do While i < 5
        i = i + 1
        MsgBox "The value of i is: " & i
    Loop
End Sub
```

`Do-While`还可以在循环结束时检查条件。循环内的语句至少执行一次，即使条件为`False`：

``` vbscript
Do
    ' User code
    [Exit Do] ' 如果需要终止循环，则使用该行代码
    ' User code
Loop While condition
```

示例如下：

``` vbscript
Private Sub Constant_demo_Click()
    i = 10

    Do
        i = i + 1
        MsgBox "The value of i is: " & i
    Loop While i < 3 ' Condition is false, hence loop is executed once
End Sub
```