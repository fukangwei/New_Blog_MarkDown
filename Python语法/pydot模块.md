---
title: pydot模块
date: 2019-03-02 18:27:33
categories: Python语法
---
&emsp;&emsp;`pydot`工具包是功能强大的`GraphViz`图形库的`Python`接口，因此需要首先安装`GraphViz`。安装`pydot`的命令如下：

``` bash
pip install pydot
```

### Example 1: Easy as Pie

&emsp;&emsp;代码如下:

``` python
import pydot
​
graph = pydot.Dot(graph_type='graph')
​
for i in range(3):
    edge = pydot.Edge("king", "lord%d" % i)
    graph.add_edge(edge)
​
vassal_num = 0
for i in range(3):
    for j in range(2):
        edge = pydot.Edge("lord%d" % i, "vassal%d" % vassal_num)
        graph.add_edge(edge)
        vassal_num += 1
​
graph.write_png('example1_graph.png')
```

<img src="./pydot模块/1.png" height="164" width="480">

### Example 2: Still Easy as Pie

&emsp;&emsp;代码如下:

``` python
import pydot
​
graph = pydot.Dot(graph_type='digraph')
node_a = pydot.Node("Node A", style="filled", fillcolor="red")
node_b = pydot.Node("Node B", style="filled", fillcolor="green")
node_c = pydot.Node("Node C", style="filled", fillcolor="#0000ff")
node_d = pydot.Node("Node D", style="filled", fillcolor="#976856")
​
graph.add_node(node_a)
graph.add_node(node_b)
graph.add_node(node_c)
graph.add_node(node_d)
​
graph.add_edge(pydot.Edge(node_a, node_b))
graph.add_edge(pydot.Edge(node_b, node_c))
graph.add_edge(pydot.Edge(node_c, node_d))
graph.add_edge(pydot.Edge(node_d, node_a, label="and back we go again", \
                          labelfontcolor="#009933", fontsize="10.0", color="blue"))
graph.write_png('example2_graph.png')
```

<img src="./pydot模块/2.png" height="240" width="162">

&emsp;&emsp;One more thing, you can set the attributes when initializing the `Node/Edge`, or you can use the `set_xyz` method, for instance, instead of doing this:

``` python
pydot.Edge(
    node_d, node_a, label="and back we go again",
    labelfontcolor="#009933", fontsize="10.0", color="blue")
```

You could do this:

``` python
edge = pydot.Edge(node_d, node_a)
edge.set_label("and back we go again")
edge.set_labelfontcolor("#009933")
edge.set_fontsize("10.0")
edge.set_color("blue")
```

---

### Graphviz安装及使用

&emsp;&emsp;下载`Windows`版本安装文件，并双击，然后一直`next`。记住安装路径，后面配置环境变量会用到该信息。
&emsp;&emsp;将`graphviz`的`bin`文件夹目录添加到系统环境变量`Path`中，进入`Windows`命令行界面，输入`dot -version`，然后按回车。如果显示`graphviz`的相关版本信息，则安装配置成功。
&emsp;&emsp;打开`graphviz`的编辑器`gvedit`，编写如下的`dot`脚本语言，保存为`test.gv`：

``` python
graph pic1 {
    a -- b
    a -- b
    b -- a [color=blue]
}
```

然后进入命令行界面，使用`dot`命令，将`gv`文件转化为`png`图形文件(也可以转换成`pdf`格式文件)：

``` bash
dot test.gv -Tpng -o image.png
```

<img src="./pydot模块/3.png">