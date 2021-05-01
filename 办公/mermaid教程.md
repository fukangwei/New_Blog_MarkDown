---
title: mermaid教程
date: 2021-04-20 21:50:31
categories: 办公
---
### 基本流程图

&emsp;&emsp;`mermaid`画出流程图的基本语法如下：<!--more-->

``` cpp
graph LR
    A --> B
    A --> C
    B --> D
    C --> D
```

结果如下：

<div align="center">

``` mermaid
graph LR
    A --> B
    A --> C
    B --> D
    C --> D
```

</div>

其中的`LR`表示流程图的方向。类似的参数还有：

参数       | 含义
-----------|-----
`TB`或`TD` | From `top` to `bottom`
`BT`       | From `bottom` to `top`
`RL`       | From `right` to `left`
`LR`       | From `left` to `right`

### 节点

&emsp;&emsp;普通节点如下：

``` cpp
graph LR
    id
```

结果如下：

<div align="center">

``` mermaid
graph LR
    id
```

</div>

&emsp;&emsp;特殊节点如下：

- `id[带文字节点]`：

<div align="center">

``` mermaid
graph LR
    id[带文字节点]
```

</div>

- `id(圆角节点)`：

<div align="center">

``` mermaid
graph LR
    id(圆角节点)
```

</div>

- `id((圆形节点))`：

<div align="center">

``` mermaid
graph LR
    id((圆形节点))
```

</div>

- `id>不对称节点]`：

<div align="center">

``` mermaid
graph LR
    id>不对称节点]
```

</div>

- `id{菱形节点}`：

<div align="center">

``` mermaid
graph LR
    id{菱形节点}
```

</div>

- `id1[(Database)]`：

<div align="center">

``` mermaid
graph LR
    id1[(Database)]
```

</div>

- `id1([This is the text in the box])`：

<div align="center">

``` mermaid
graph LR
    id1([This is the text in the box])
```

</div>

- `id1[[This is the text in the box]]`：

<div align="center">

``` mermaid
graph LR
    id1[[This is the text in the box]]
```

</div>

### 连接线

&emsp;&emsp;`mermaid`可以使用如下类型的连接线：

- `A --> B`：

<div align="center">

``` mermaid
graph LR
    A --> B
```

</div>

- `A --- B`：

<div align="center">

``` mermaid
graph LR
    A --- B
```

</div>

- `A ---|文字| B`：

<div align="center">

``` mermaid
graph LR
    A ---|文字| B
```

</div>

- `A -->|文字| B`：

<div align="center">

``` mermaid
graph LR
    A -->|文字| B
```

</div>

- `A -.-> B`：

<div align="center">

``` mermaid
graph LR
    A-.->B
```

</div>

- `A -.text.-> B`：

<div align="center">

``` mermaid
graph LR
    A-.text.->B
```

</div>

- `A ==> B`：

<div align="center">

``` mermaid
graph LR
    A ==> B
```

</div>

- `A == text ==> B`：

<div align="center">

``` mermaid
graph LR
   A == text ==> B
```

</div>

&emsp;&emsp;`mermaid`还可以让某些连接线长于其他的连接线：

``` cpp
graph TD
    A[Start] --> B{Is it?}
    B -->|Yes| C[OK]
    C --> D[Rethink]
    D --> B
    B ---->|No| E[End]
```

结果如下：

<div align="center">

``` mermaid
graph LR
    A[Start] --> B{Is it?}
    B -->|Yes| C[OK]
    C --> D[Rethink]
    D --> B
    B ---->|No| E[End]
```

</div>

&emsp;&emsp;`mermaid`可以在一行中声明多个连接：

- `A -- text --> B -- text2 --> C`：

<div align="center">

``` mermaid
graph LR
    A -- text --> B -- text2 --> C
```

</div>

- `a --> b & c--> d`：

<div align="center">

``` mermaid
graph LR
    a --> b & c--> d
```

</div>

- `A & B --> C & D`：

<div align="center">

``` mermaid
graph TB
    A & B--> C & D
```

</div>

### 特殊的语法

&emsp;&emsp;使用引号可以抑制一些特殊的字符的使用，可以避免一些不必要的麻烦：

``` cpp
graph LR
    d1["This is the (text) in the box"]
```

结果如下：

<div align="center">

``` mermaid
graph LR
    d1["This is the (text) in the box"]
```

</div>

### Html字符的转义字符

&emsp;&emsp;转义字符的使用语法如下：

``` cpp
graph LR
    A["A double quote:#quot;"] --> B["A dec char:#9829;"]
```

结果如下：

<div align="center">

``` mermaid
graph LR
    A["A double quote:#quot;"] --> B["A dec char:#9829;"]
```

</div>

### 子图

&emsp;&emsp;子图的使用语法如下：

``` cpp
graph TB
    subgraph one
        a1 --> a2
    end

    subgraph two
        b2 --> b2
    end

    subgraph three
        c1 --> c2
    end

    c1 --> a2
```

结果如下：

<div align="center">

``` mermaid
graph TB
    subgraph one
        a1 --> a2
    end

    subgraph two
        b2 --> b2
    end

    subgraph three
        c1 --> c2
    end

    c1 --> a2
```

</div>

&emsp;&emsp;另一种语法如下：

``` cpp
graph LR
    subgraph Understand
        D[Transform]
        E[Visualise]
        F[Model]
    end

    A --> D
    D --> E
    F --> D
    E --> F
    F --> AA
```

结果如下：

<div align="center">

``` mermaid
graph LR
    subgraph Understand
        D[Transform]
        E[Visualise]
        F[Model]
    end

    A --> D
    D --> E
    F --> D
    E --> F
    F --> AA
```

</div>

&emsp;&emsp;也可以使用`flowchart`来绘制子图：

``` cpp
flowchart TB
    c1 --> a2

    subgraph one
        a1 --> a2
    end

    subgraph two
        b1 --> b2
    end

    subgraph three
        c1 --> c2
    end

    one --> two
    three --> two
    two --> c2
```

结果如下：

<div align="center">

``` mermaid
flowchart TB
    c1 --> a2

    subgraph one
        a1 --> a2
    end

    subgraph two
        b1 --> b2
    end

    subgraph three
        c1 --> c2
    end

    one --> two
    three --> two
    two --> c2
```

</div>

### 链接

&emsp;&emsp;为节点增加链接的方法如下：

``` cpp
graph LR
    A --> B
    B --> C
    click B "http://www.github.com"
```

结果如下：

<div align="center">

``` mermaid
graph LR
    A --> B
    B --> C
    click B "http://www.github.com"
```

</div>

### 注释

&emsp;&emsp;对代码进行注释的方法如下：

``` cpp
graph LR
    %% this is a comment
    A -- text --> B -- text2 --> C
```

结果如下：

<div align="center">

``` mermaid
graph LR
    %% this is a comment
    A -- text --> B -- text2 --> C
```

</div>

### 序列图

&emsp;&emsp;画出序列图的方法如下：

``` cpp
sequenceDiagram
    A ->   B : 无箭头实线
    A -->  B : 无箭头虚线(点线)
    A ->>  B : 有箭头实线
    A -->> B : 有箭头实线
    A -x   B : 有箭头实线，加上叉
    A --x  B : 有箭头虚线，加上叉
```

结果如下：

<div align="center">

``` mermaid
sequenceDiagram
    A ->   B : 无箭头实线
    A -->  B : 无箭头虚线(点线)
    A ->>  B : 有箭头实线
    A -->> B : 有箭头实线
    A -x   B : 有箭头实线，加上叉
    A --x  B : 有箭头虚线，加上叉
```

</div>

&emsp;&emsp;如果想让`John`出现在前面，可以让`mermaid`通过设定参与者(`participants`)来实现：

``` cpp
sequenceDiagram
    participant John
    participant Alice
    Alice -x   John : Hello John, how are you?
    John  -->> Alice : Great!
```

结果如下：

<div align="center">

``` mermaid
sequenceDiagram
    participant John
    participant Alice
    Alice -x   John : Hello John, how are you?
    John  -->> Alice : Great!
```

</div>

#### 别名

&emsp;&emsp;可以给角色起一个简短的别名，以方便书写：

``` cpp
sequenceDiagram
    participant A as Alice
    participant J as John
    A ->> J : Hello John, how are you?
    J ->> A : Great!
```

<div align="center">

``` mermaid
sequenceDiagram
    participant A as Alice
    participant J as John
    A ->> J : Hello John, how are you?
    J ->> A : Great!
```

</div>

#### 便签

&emsp;&emsp;给序列图增加便签的语法如下：

``` cpp
sequenceDiagram
    participant John
    Note left of John : Text in note
```

代码如下：

<div align="center">

``` mermaid
sequenceDiagram
    participant John
    Note left of John : Text in note
```

</div>

&emsp;&emsp;跨越两个`Actor`的便签：

``` cpp
sequenceDiagram
    Alice -> John : Hello John, how are you?
    Note over Alice, John : A typical interaction
```

代码如下：

<div align="center">

``` mermaid
sequenceDiagram
    Alice -> John : Hello John, how are you?
    Note over Alice, John : A typical interaction
```

</div>

&emsp;&emsp;只在一个`Actor`上的便签：

``` cpp
sequenceDiagram
    Alice -> John : Hello John, how are you?
    Note over Alice : Ha Ha Ha
```

结果如下：

<div align="center">

``` mermaid
sequenceDiagram
    Alice -> John : Hello John, how are you?
    Note over Alice : Ha Ha Ha
```

</div>

#### 循环

&emsp;&emsp;在序列图中也可以使用循环：

``` cpp
sequenceDiagram
    Alice ->> John : Hello!
    loop Reply every minute
        John ->> Alice : Great!
    end
```

结果如下：

<div align="center">

``` mermaid
sequenceDiagram
    Alice ->> John : Hello!
    loop Reply every minute
        John ->> Alice : Great!
    end
```

</div>

### 分支选择

&emsp;&emsp;在序列图中也可以进行分支选择：

``` cpp
sequenceDiagram
    Alice ->> Bob : Hello Bob, how are you?

    %% 适用于处理多个分支
    alt is sick
        Bob ->> Alice : not so good :(
    else is well
        Bob ->> Alice : Feeling fresh like a daisy :)
    end

    %% opt适用于可选的分支
    opt Extra response
        Bob ->> Alice : Thanks for asking
    end
```

结果如下：

<div align="center">

``` mermaid
sequenceDiagram
    Alice ->> Bob : Hello Bob, how are you?

    %% 适用于处理多个分支
    alt is sick
        Bob ->> Alice : not so good :(
    else is well
        Bob ->> Alice : Feeling fresh like a daisy :)
    end

    %% opt适用于可选的分支
    opt Extra response
        Bob ->> Alice : Thanks for asking
    end
```

</div>

### 区域高亮

&emsp;&emsp;在序列图中也可以进行区域高亮：

``` cpp
sequenceDiagram
rect rgba(0, 255, 255, .6)
    Bob ->> Alice : Thanks for asking
end
```

结果如下：

<div align="center">

``` mermaid
sequenceDiagram
rect rgba(0, 255, 255, .6)
    Bob ->> Alice : Thanks for asking
end
```

</div>

### 饼图

&emsp;&emsp;饼图的代码如下：

``` cpp
pie
    title 电影
    "喜剧"   : 38
    "恐怖片" : 85
    "动作片" : 100
    "音乐剧" : 150 
```

结果如下：

<div align="center">

``` mermaid
pie
    title 电影
    "喜剧"   : 38
    "恐怖片" : 85
    "动作片" : 100
    "音乐剧" : 150 
```

</div>