---
title: sftp下载器
date: 2018-12-29 08:00:34
categories: Python应用代码
---
&emsp;&emsp;代码如下：

``` python
from tkinter import *
import paramiko
​
current_dir = "/home/user4"  # 当前的目录
​
def enter_dir(event):  # 双击进入下一个目录
    global current_dir

    if dict[theLB.get(theLB.curselection())] == "dir":
        print("这是一个目录")
        dir_name = theLB.get(theLB.curselection())
        current_dir = current_dir + "/" + dir_name
    elif dict[theLB.get(theLB.curselection())] == "file":
        print("这是一个文件")
        return

    show_current_list()
​
def dir_return():  # 显示当前的目录
    global current_dir
    current_dir = current_dir[0:current_dir.rindex("/")]
    show_current_list()
​
dict = {}  # 创建文件空字典
​
def show_current_list():  # 显示当前的目录
    global dict
    dict.clear()  # 清空字典
    stdin, stdout, stderr = ssh.exec_command("cd " + current_dir + ";ls -al")
    file_list = stdout.readlines()
    theLB.delete(0, END)  # 删除原来的所有项目

    for i in file_list:
        if i.rstrip()[0] == 'd':  # 如果是目录
            dict[i.rstrip().split(" ")[-1]] = "dir"
        elif i.rstrip()[0] == '-':  # 如果是文件
            dict[i.rstrip().split(" ")[-1]] = "file"
​
    for i in dict.keys():  # 将目录和文件的名称插入到listbox中
        theLB.insert(END, i)
​
def download():
    global current_dir
    if dict[theLB.get(theLB.curselection())] == "dir":  # 如果当前选定的是目录
        return
    elif dict[theLB.get(theLB.curselection())] == "file":  # 如果当前选定的是文件
        file_name = current_dir + "/" + theLB.get(theLB.curselection())
        print(file_name)
​
    remotepath = file_name  # 获取远程访问的文件名
    localpath = theLB.get(theLB.curselection())  # 保存至本地的文件名
    sftp.get(remotepath, localpath)
​
if __name__ == "__main__":
    trans = paramiko.Transport(("58.193.165.12", 22))
    trans.connect(username="user4", password="444444")
    sftp = paramiko.SFTPClient.from_transport(trans)
    ssh = paramiko.SSHClient()
    ssh._transport = trans
    root = Tk()
    Label(root, text="下载文件路径：").grid(row=0, column=0)
​
    e1 = Entry(root)
    Button(root, text="下载文件", width=10, command=download).grid(row=2, column=0, sticky=W, padx=10, pady=5)
    Button(root, text="返回上一个目录", width=10, command=dir_return).grid(row=3, column=0, sticky=W, padx=10, pady=5)
​
    theLB = Listbox(root, selectmode=BROWSE)  # 创建一个空列表
    theLB.grid(row=1, column=0)
    theLB.bind('<Double-Button-1>', enter_dir)  # 创建双击列表中元素的事件，即进入该目录
    show_current_list()
​
    mainloop()
    trans.close()
```