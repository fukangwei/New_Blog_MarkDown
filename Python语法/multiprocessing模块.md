---
title: multiprocessing模块
categories: Python语法
date: 2019-02-10 16:54:23
---
&emsp;&emsp;`Python`提供了多进程模块`multiprocessing`，它支持子进程调度、通信以及共享内存，提供了`Process`、`Lock`等组件。<!--more-->
&emsp;&emsp;多进程和多线程区别：

- 多线程使用的是`CPU`的一个核，适合`IO`密集型任务。
- 多进程使用的是`CPU`的多个核，适合运算密集型任务。

### multiprocessing的方法

&emsp;&emsp;`multiprocessing`的方法如下：

- `cpu_count`：统计`cpu`总数。
- `active_children`：获取所有子进程。

``` python
import multiprocessing

p = multiprocessing.cpu_count()
m = multiprocessing.active_children()

print(p)  # 输出“8”
print(m)  # 输出“[]”
```

### Process进程

&emsp;&emsp;创建一个`Process`对象：

``` python
p = multiprocessing.Precess(target=worker, args=(2,))
```

- `target`是函数名字。
- `args`是函数需要的的参数，以`tuple`形式传入。

&emsp;&emsp;`Process`的常用方法如下：

- `is_alive`：判断进程是否存活。
- `run`：启动进程。
- `start`：启动进程，会自动调用`run`方法。
- `join(timeout=)`：等待进程结束，或者直到超时。

&emsp;&emsp;`Process`的常用属性如下：

- `name`：进程名字。
- `pid`：进程的`pid`。

``` python
import time
import multiprocessing

def worker(interval):
    print("interval is", interval)
    time.sleep(interval)

if __name__ == "__main__":
    p = multiprocessing.Process(target=worker, args=(5,))
    p.start()
    print(p.is_alive())
    p.join(timeout=3)  # 只等待3秒，如果进程还没结束，则向下执行print(p.name)
    print(p.name)
    print(p.pid)
    print("This is end")
```

执行结果：

``` python
True
interval is 5
Process-1
9572
This is end
```

### 多进程实例

&emsp;&emsp;代码如下：

``` python
import time
import multiprocessing

def worker(name, interval):
    print("{0} start".format(name))
    time.sleep(interval)
    print("{0} end".format(name))

if __name__ == "__main__":
    print("main start")
    print("The computer has {0} core".format(multiprocessing.cpu_count()))

    p1 = multiprocessing.Process(target=worker, args=("worker", 2))
    p2 = multiprocessing.Process(target=worker, args=("worker", 3))
    p3 = multiprocessing.Process(target=worker, args=("worker", 4))

    p1.start()
    p2.start()
    p3.start()

    for p in multiprocessing.active_children():
        print("The pid of {0} is {1}".format(p.name, p.pid))

    print("main end")
```

执行结果：

``` python
main start
The computer has 8 core
worker start
The pid of Process-1 is 5400
The pid of Process-2 is 3492
The pid of Process-3 is 11348
main end
worker start
worker start
worker end
worker end
worker end
```

这说明启动的多个进程之间都是相互独立存在的。

### lock组件

&emsp;&emsp;当使用多进程来读写文件时，需要用`锁机制`对读写进行控制：

``` python
import multiprocessing
import time

def add(number, value, lock):
    lock.acquire()  # 获取锁

    try:
        print("init, member = {1}".format(value, number))

        for i in range(1, 3):
            number += value
            time.sleep(1)
            print("add {0}, number = {1}".format(value, number))
    except Exception as e:
        raise e
    finally:
        lock.release()  # 释放锁

if __name__ == "__main__":
    lock = multiprocessing.Lock()  # 定义锁
    number = 0

    p1 = multiprocessing.Process(target=add, args=(number, 1, lock))
    p3 = multiprocessing.Process(target=add, args=(number, 3, lock))

    p1.start()
    p3.start()

    print("main end")
```

执行结果：

``` python
main end
init, member = 0
add 1, number = 1
add 1, number = 2
init, member = 0
add 3, number = 3
add 3, number = 6
```

### 共享内存

&emsp;&emsp;`multiprocessing`提供了`Value`和`Array`模块来实现共享内存的读写：

``` python
import multiprocessing
import time

def add(number, value1, lock):
    lock.acquire()

    try:
        print("init, member = {1}".format(value1, number.value))

        for i in range(1, 3):
            number.value += value1
            time.sleep(1)
            print("add {0}, number = {1}".format(value1, number.value))
    except Exception as e:
        raise e
    finally:
        lock.release()

if __name__ == "__main__":
    lock = multiprocessing.Lock()
    number = multiprocessing.Value("i", 0)

    p1 = multiprocessing.Process(target=add, args=(number, 1, lock))
    p3 = multiprocessing.Process(target=add, args=(number, 3, lock))

    p1.start()
    p3.start()

    print("main end")
```

执行结果：

``` python
main end
init, member = 0
add 1, number = 1
add 1, number = 2
init, member = 2
add 3, number = 5
add 3, number = 8
```

### 进程池

&emsp;&emsp;`Pool`可以提供指定数量的进程供用户调用。当有新的请求提交到`pool`中时：

1. 如果池还没有满，那么就会创建一个新的进程用来执行该请求。
2. 如果池中的进程数已经达到规定最大值，那么该请求就会等待，直到池中有进程结束，才会创建新的进程。

&emsp;&emsp;进程有`阻塞`和`非阻塞`这`2`种类型：

- `Pool.apply_async`：非阻塞型，进程可以同时执行。
- `Pool.apply`：阻塞型，一个进程结束并释放回进程池，下一个进程才可以开始。

#### 非阻塞型

&emsp;&emsp;非阻塞型如下：

``` python
import multiprocessing
import time

def worker(msg):
    print("start {0}".format(msg))
    time.sleep(1)
    print("end {0}".format(msg))

if __name__ == "__main__":
    print("main start")
    pool = multiprocessing.Pool(processes=3)

    for i in range(1, 5):
        msg = "hello {0}".format(i)
        pool.apply_async(func=worker, args=(msg,))

    pool.close()
    pool.join()
    print("main end")
```

调用`join`之前，先调用`close`函数，否则会出错。执行完`close`后，如果没有新的进程加入到`pool`，则`join`函数等待所有子进程结束。执行结果：

``` python
main start
start hello 1
start hello 2
start hello 3
end hello 1
start hello 4
end hello 2
end hello 3
end hello 4
main end
```

#### 阻塞型

&emsp;&emsp;阻塞型如下：

``` python
import multiprocessing
import time

def worker(msg):
    print("start {0}".format(msg))
    time.sleep(1)
    print("end {0}".format(msg))

if __name__ == "__main__":
    print("main start")
    pool = multiprocessing.Pool(processes=3)

    for i in range(1, 5):
        msg = "hello {0}".format(i)
        pool.apply(func=worker, args=(msg,))

    pool.close()
    pool.join()
    print("main end")
```

执行结果：

``` python
main start
start hello 1
end hello 1
start hello 2
end hello 2
start hello 3
end hello 3
start hello 4
end hello 4
main end
```