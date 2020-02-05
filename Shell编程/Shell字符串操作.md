---
title: Shell字符串操作
categories: Shell编程
abbrlink: 3ab3bc10
date: 2019-01-19 19:23:09
---
&emsp;&emsp;读取字符串值：<!--more-->

表达式             | 含义
------------------|-----
`${var}`          | 变量`var`的值，与`$var`相同
`${var-DEFAULT}`  | 如果`var`没有被声明，那么就以`$DEFAULT`作为其值
`${var:-DEFAULT}` | 如果`var`没有被声明，或者其值为空，那么就以`$DEFAULT`作为其值
`${var=DEFAULT}`  | 如果`var`没有被声明，那么就以`$DEFAULT`作为其值
`${var:=DEFAULT}` | 如果`var`没有被声明，或者其值为空，那么就以`$DEFAULT`作为其值
`${var+OTHER}`    | 如果`var`被声明了，那么其值就是`$OTHER`，否则就为`null`字符串
`${var:+OTHER}`   | 如果`var`被设置了，那么其值就是`$OTHER`，否则就为`null`字符串
`${var?ERR_MSG}`  | 如果`var`没被声明，那么就打印`$ERR_MSG`
`${var:?ERR_MSG}` | 如果`var`没被设置，那么就打印`$ERR_MSG`
`${!varprefix*}`  | 匹配之前所有以`varprefix`开头进行声明的变量
`${!varprefix@}`  | 匹配之前所有以`varprefix`开头进行声明的变量

&emsp;&emsp;字符串操作：

表达式                             | 含义
-----------------------------------|-------
<code>${&#35;string}</code>        | `$string`的长度
`${string:position}`               | 在`$string`中，从位置`$position`开始提取子串
`${string:position:length}`        | 在`$string`中，从位置`$position`开始提取长度为`$length`的子串
`${string#substring}`              | 从变量`$string`的开头，删除最短匹配`$substring`的子串
`${string##substring}`             | 从变量`$string`的开头，删除最长匹配`$substring`的子串
`${string%substring}`              | 从变量`$string`的结尾，删除最短匹配`$substring`的子串
`${string%%substring}`             | 从变量`$string`的结尾，删除最长匹配`$substring`的子串
`${string/substring/replacement}`  | 使用`$replacement`，来代替第一个匹配的`$substring`
`${string//substring/replacement}` | 使用`$replacement`，代替所有匹配的`$substring`
`${string/#substring/replacement}` | 如果`$string`的前缀匹配`$substring`，那么就用`$replacement`来代替匹配到的`$substring`
`${string/%substring/replacement}` | 如果`$string`的后缀匹配`$substring`，那么就用`$replacement`来代替匹配到的`$substring`