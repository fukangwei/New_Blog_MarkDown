---
title: psutil模块
categories: Python语法
date: 2019-02-10 17:08:38
---
&emsp;&emsp;`psutil`模块用于获取系统信息。<!--more-->

### 获取CPU信息

&emsp;&emsp;获取`CPU`的信息：

``` python
>>> import psutil
>>> psutil.cpu_count()  # CPU逻辑数量
4
>>> psutil.cpu_count(logical=False)  # CPU物理核心
2
```

&emsp;&emsp;统计`CPU`的`用户/系统/空闲时间`：

``` python
>>> psutil.cpu_times()
scputimes(user=10963.31, nice=0.0, system=5138.67, idle=356102.45)
```

&emsp;&emsp;查看`CPU`使用率，每秒刷新一次，累计`10`次：

``` python
>>> for x in range(10):
...     psutil.cpu_percent(interval=1, percpu=True)
...
[14.0, 4.0, 4.0, 4.0]
[12.0, 3.0, 4.0, 3.0]
[8.0, 4.0, 3.0, 4.0]
[12.0, 3.0, 3.0, 3.0]
[18.8, 5.1, 5.9, 5.0]
[10.9, 5.0, 4.0, 3.0]
[12.0, 5.0, 4.0, 5.0]
[15.0, 5.0, 4.0, 4.0]
[19.0, 5.0, 5.0, 4.0]
[9.0, 3.0, 2.0, 3.0]
```

### 获取内存信息

&emsp;&emsp;获取物理内存和交换内存信息：

``` python
>>> psutil.virtual_memory()
svmem(total=8589934592, available=2866520064, percent=66.6, used=7201386496, \
...   free=216178688, active=3342192640, inactive=2650341376, wired=1208852480)
>>> psutil.swap_memory()
sswap(total=1073741824, used=150732800, free=923009024, percent=14.0, sin=10705981440, sout=40353792)
```

### 获取磁盘信息

&emsp;&emsp;获取磁盘分区、磁盘使用率、磁盘`IO`信息：

``` python
>>> psutil.disk_partitions()  # 磁盘分区信息
[sdiskpart(device='/dev/disk1', mountpoint='/', fstype='hfs', \
...        opts='rw,local,rootfs,dovolfs,journaled,multilabel')]
>>> psutil.disk_usage('/')  # 磁盘使用情况
sdiskusage(total=998982549504, used=390880133120, free=607840272384, percent=39.1)
>>> psutil.disk_io_counters()  # 磁盘IO
sdiskio(read_count=988513, write_count=274457, read_bytes=14856830464, \
...     write_bytes=17509420032, read_time=2228966, write_time=1618405)
```

### 获取网络信息

&emsp;&emsp;获取网络接口信息：

``` python
>>> psutil.net_io_counters()  # 获取网络读写字节的个数
snetio(bytes_sent=3885744870, bytes_recv=10357676702, packets_sent=10613069, \
...    packets_recv=10423357, errin=0, errout=0, dropin=0, dropout=0)
>>> psutil.net_if_addrs()  # 获取网络接口信息
{
  'lo0': [snic(family=<AddressFamily.AF_INET: 2>, address='127.0.0.1', netmask='255.0.0.0'), ...],
  'en1': [snic(family=<AddressFamily.AF_INET: 2>, address='10.0.1.80', netmask='255.255.255.0'), ...],
  'en0': [...],
  'en2': [...],
  'bridge0': [...]
}
>>> psutil.net_if_stats()  # 获取网络接口状态
{
  'lo0': snicstats(isup=True, duplex=<NicDuplex.NIC_DUPLEX_UNKNOWN: 0>, speed=0, mtu=16384),
  'en0': snicstats(isup=True, duplex=<NicDuplex.NIC_DUPLEX_UNKNOWN: 0>, speed=0, mtu=1500),
  'en1': snicstats(...),
  'en2': snicstats(...),
  'bridge0': snicstats(...)
}
```

&emsp;&emsp;获取网络连接信息需要`root`权限：

``` python
>>> import psutil
>>> psutil.net_connections()
[
    sconn(fd=83, family=<AddressFamily.AF_INET6: 30>, type=1, laddr=addr(ip='::127.0.0.1', port=62911), \
    raddr=addr(ip='::127.0.0.1', port=3306), status='ESTABLISHED', pid=3725), \
    sconn(fd=84, family=<AddressFamily.AF_INET6: 30>, type=1, laddr=addr(ip='::127.0.0.1', port=62905), \
    raddr=addr(ip='::127.0.0.1', port=3306), status='ESTABLISHED', pid=3725),
    sconn(fd=93, family=<AddressFamily.AF_INET6: 30>, type=1, laddr=addr(ip='::', port=8080), \
    raddr=(), status='LISTEN', pid=3725), sconn(fd=103, family=<AddressFamily.AF_INET6: 30>, type=1, \
    laddr=addr(ip='::127.0.0.1', port=62918), raddr=addr(ip='::127.0.0.1', port=3306), \
               status='ESTABLISHED', pid=3725),
    sconn(fd=105, family=<AddressFamily.AF_INET6: 30>, type=1, ..., pid=3725),
    sconn(fd=106, family=<AddressFamily.AF_INET6: 30>, type=1, ..., pid=3725),
    sconn(fd=107, family=<AddressFamily.AF_INET6: 30>, type=1, ..., pid=3725),
    ...
    sconn(fd=27, family=<AddressFamily.AF_INET: 2>, type=2, ..., pid=1)
]
```

### 获取进程信息

&emsp;&emsp;获取所有进程的详细信息：

``` python
>>> psutil.pids()  # 所有进程ID
[3865, 3864, 3863, 3856, 3855, 3853, 3776, ..., 45, 44, 1, 0]
>>> p = psutil.Process(3776)  # 获取指定进程(ID = 3776)，其实就是当前Python交互环境
>>> p.name()  # 进程名称
'python3.6'
>>> p.exe()  # 进程exe路径
'/Users/michael/anaconda3/bin/python3.6'
>>> p.cwd()  # 进程工作目录
'/Users/michael'
>>> p.cmdline()  # 进程启动的命令行
['python3']
>>> p.ppid()  # 父进程ID
3765
>>> p.parent()  # 父进程
<psutil.Process(pid=3765, name='bash') at 4503144040>
>>> p.children()  # 子进程列表
[]
>>> p.status()  # 进程状态
'running'
>>> p.username()  # 进程用户名
'michael'
>>> p.create_time()  # 进程创建时间
1511052731.120333
>>> p.terminal()  # 进程终端
'/dev/ttys002'
>>> p.cpu_times()  # 进程使用的CPU时间
pcputimes(user=0.081150144, system=0.053269812, children_user=0.0, children_system=0.0)
>>> p.memory_info()  # 进程使用的内存
pmem(rss=8310784, vms=2481725440, pfaults=3207, pageins=18)
>>> p.open_files()  # 进程打开的文件
[]
>>> p.connections()  # 进程相关网络连接
[]
>>> p.num_threads()  # 进程的线程数量
1
>>> p.threads()  # 所有线程信息
[pthread(id=1, user_time=0.090318, system_time=0.062736)]
>>> p.environ()  # 进程环境变量
{'SHELL': '/bin/bash', 'PATH': '/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:...', \
 'PWD': '/Users/michael', 'LANG': 'zh_CN.UTF-8', ...}
>>> p.terminate()  # 结束进程
Terminated: 15 <-- 自己把自己结束了
```