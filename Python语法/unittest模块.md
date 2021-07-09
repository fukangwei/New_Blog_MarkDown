---
title: unittest模块
categories: Python语法
date: 2019-02-10 08:51:32
---
### 单元测试

&emsp;&emsp;单元测试用于对一个模块、一个函数或者一个类进行正确性的检验。<!--more-->
&emsp;&emsp;例如，对于函数`abs`，我们可以编写以下测试情形：

- 输入正数，比如`1`、`1.2`、`0.99`，期待返回值与输入相同。
- 输入负数，比如`-1`、`-1.2`、`-0.99`，期待返回值与输入相反。
- 输入`0`，期待返回`0`。
- 输入非数值类型，比如`None`、`[]`、`{}`，期待抛出`TypeError`。

把上面的测试用例放到一个测试模块里，就是一个完整的单元测试。

### 测试实例

&emsp;&emsp;编写一个`Dict`类，这个类的行为和`dict`一致，但是可以通过属性来访问：

``` python
>>> d = Dict(a=1, b=2)
>>> d['a']
1
>>> d.a
1
```

&emsp;&emsp;`mydict.py`如下：

``` python
class Dict(dict):
    def __init__(self, **kw):
        super(Dict, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value
```

&emsp;&emsp;`mydict_test.py`如下：

``` python
import unittest
from mydict import Dict

class TestDict(unittest.TestCase):
    def test_init(self):
        d = Dict(a=1, b='test')
        self.assertEqual(d.a, 1)
        self.assertEqual(d.b, 'test')
        self.assertTrue(isinstance(d, dict))

    def test_key(self):
        d = Dict()
        d['key'] = 'value'
        self.assertEqual(d.key, 'value')

    def test_attr(self):
        d = Dict()
        d.key = 'value'
        self.assertTrue('key' in d)
        self.assertEqual(d['key'], 'value')

    def test_keyerror(self):
        d = Dict()

        with self.assertRaises(KeyError):
            value = d['empty']

    def test_attrerror(self):
        d = Dict()

        with self.assertRaises(AttributeError):
            value = d.empty
```

&emsp;&emsp;编写单元测试时，需要编写一个测试类，该类从`unittest.TestCase`继承。
&emsp;&emsp;以`test`开头的方法就是测试方法，不以`test`开头的方法不被认为是测试方法，测试的时候不会被执行。

### 条件断言

&emsp;&emsp;`unittest.TestCase`提供了很多内置的断言，最常用的断言就是`assertEqual`：

``` python
self.assertEqual(abs(-1), 1)  # 断言函数返回的结果与“1”相等
```

&emsp;&emsp;另一种重要的断言就是期待抛出指定类型的`Error`，例如通过`d['empty']`访问不存在的`key`时，断言会抛出`KeyError`：

``` python
with self.assertRaises(KeyError):
    value = d['empty']
```

&emsp;&emsp;通过`d.empty`访问不存在的`key`时，期待抛出`AttributeError`：

``` python
with self.assertRaises(AttributeError):
    value = d.empty
```

### 运行单元测试

&emsp;&emsp;最简单的运行方式是在`mydict_test.py`的最后加上两行代码：

``` python
if __name__ == '__main__':
    unittest.main()
```

这样就可以把`mydict_test.py`当做正常的`python`脚本运行：

``` bash
python mydict_test.py
```

&emsp;&emsp;更常见的方法是在命令行使用参数`-m unittest`：

``` bash
python -m unittest mydict_test
```

### setUp与tearDown

&emsp;&emsp;可以在单元测试中编写`setUp`和`tearDown`，这两个方法会在每次调用一个测试方法的前后分别被执行：

``` python
class TestDict(unittest.TestCase):
    def setUp(self):
        print('setUp...')

    def tearDown(self):
        print('tearDown...')
```

### 跳过某个case

&emsp;&emsp;如果需要临时跳过某个`case`，可以使用`skip`装饰器：

``` python
class TestMathFunc(unittest.TestCase):
    """ Test mathfuc.py """
    ...
    @unittest.skip("I don't want to run this case.")
    def test_divide(self):
        """ Test method divide(a, b) """
        print("divide")
        self.assertEqual(2, divide(6, 3))
        self.assertEqual(2.5, divide(5, 2))
```

### setUpClass和tearDownClass

&emsp;&emsp;可以使用`setUpClass`、`tearDownClass`做所有`case`的前置初始化和结束。
&emsp;&emsp;以下程序的执行顺序为`setUpClass -> test_case1 -> test_case2 -> tearDownClass`：

``` python
import unittest

class MyTestCase(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        print('开始执行')

    @classmethod
    def tearDownClass(cls):
        print('结束执行')

    def test_case1(self):  # 测试用例1
        print('用例1')

    def test_case2(self):  # 测试用例2
        print('用例2')

if __name__ == '__main__':
    unittest.main()
```