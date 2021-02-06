---
title: ubuntu服务器
categories: 软件与硬件问题
date: 2019-02-14 16:37:15
---
### FTP服务器

&emsp;&emsp;在`Linux`系统中，`ftp`服务器的全名叫`vsftpd`。我们需要利用相关命令来安装`ftp`服务器，然后在`vsftpd.conf`中进行相关配置。<!--more-->
&emsp;&emsp;1. 首先使用如下命令进行安装：

``` bash
sudo apt-get install vsftpd
```

安装完成后，输入`vsftpd -version`查看是否安装成功。
&emsp;&emsp;2. 新建一个用于`FTP`的工作目录：

``` bash
mkdir /home/ftp
```

&emsp;&emsp;3. 新建`FTP`用户，并设置密码以及工作目录，其中`ftpname`是创建的用户名：

``` bash
sudo useradd -d /home/ftp -s /bin/bash ftpname
```

为新建的用户设置密码：

``` bash
passwd ftpname
```

可以使用`cat /etc/passwd`查看当前系统用户。
&emsp;&emsp;4. 修改`vsftpd`配置文件，该文件位于`/etc`目录下，相关的配置如下：

``` bash
listen=YES                     # 服务器监听
anonymous_enable=NO            # 匿名访问允许。此选项很危险，默认不要开启
local_enable=YES               # 本地主机访问允许
write_enable=YES               # 写允许
# anon_upload_enable=YES       # 匿名上传允许
# anon_mkdir_write_enable=YES  # 匿名创建文件夹允许
dirmessage_enable=YES          # 进入文件夹允许
xferlog_enable=YES             # ftp日志记录允许
connect_from_port_20=YES       # 允许使用端口号20作为数据传送的端口
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd
rsa_cert_file=/etc/ssl/private/vsftpd.pem
local_root=/home/ftp           # 设置ftp的根目录为“/home/ftp”
```

设置好之后，保存退出，然后使用如下命令重启`ftp`服务器：

``` bash
sudo service vsftpd restart
```

&emsp;&emsp;5. 在浏览器中的地址栏输入`ftp://ftp服务器地址`，然后输入账号和密码即可实现登录。

#### vsftp配置参数说明

&emsp;&emsp;`vsftp`的各配置参数如下：

参数                                          | 说明
----------------------------------------------|----------------
`anonymous_enable=YES`                        | 是否开启匿名用户
`no_anon_password=YES`                        | 匿名用户`login`时不询问用户名和口令
`anon_umask=077`                              | 匿名用户上传的文件权限
`anon_upload_enable=YES`                      | 是否允许匿名用户上传文件
`anon_mkdir_write_enable=YES`                 | 是否允许匿名用户建立目录
`anon_other_write_enable=YES`                 | 是否允许匿名用户具有建立目录、上传之外的权限，如重命名、删除
`anon_world_readable_only=YES`                | 匿名登入者是否能下载可阅读的档案
`anon_max_rate=80000`                         | 匿名用户的下载速度为`80KB/s`
`anon_root=(none)`                            | 匿名用户的宿主目录
`allow_anon_ssl=NO`                           | 匿名用户是否允许使用安全的`SSL`连接服务器
`ftp_username=FTP`                            | 定义匿名使用者登录的使用者名称(默认为`FTP`)
`banned_email_file=/etc/vsftpd.banned_emails` | 禁止使用的匿名用户登陆时作为密码的电子邮件地址使用表的位置
`deny_email_enable=NO`                        | 禁止使用的匿名用户登陆时作为密码的电子邮件地址
`secure_email_list_enable`                    | 如果你想只接受以指定`E-MAIL`地址登录的匿名用户的话，启用它
`local_enable=YES`                            | 本地用户是否可以登录
`local_umask=022`                             | 设置本地用户的文件生成掩码
`file_open_mode=0666`                         | 上传文件的权限配合`umask`使用
`local_root=(none)`                           | 指定所有本地用户登陆后的目录，如果不设置此项，用户都会登陆于自己的主目录
`local_max_rate=500000`                       | 本地用户的下载速度为`500KB/s`
`chroot_local_user=YES`                       | 是否允许用户离开其宿主目录
`chroot_list_enable=NO`                       | 登录用户名字若在`/etc/vsftpd.chroot_list`内，则会启用`chroot`机制，将这个用户限制在其`home`目录下
`guest_enable=YES`                            | 是否开启虚拟用户
`guest_username=vsftpd`                       | 指定虚拟用户名
`virtual_use_local_privs=YES`                 | 虚拟用户和本地用户权限是否相同
`userlist_enable=YES`                         | 是否根据`user_list`实行访问控制(若启用此选项，`userlist_deny`选项才被启动)
`userlist_deny=NO`                            | 若为`YES`，则`userlist_file`中的用户将不能登录；为`NO`，则只有`userlist_file`的用户可以登录
`write_enable=YES`                            | 用户是否具有写的权限
`download_enable=YES`                         | 是否允许下载
`chmod_enable=YES`                            | 是否可以修改文件权限
`nopriv_user= nobody`                         | 设定服务执行者为`nobody`，`vsftpd`推荐使用一个权限很低的用户，最好是没有家目录(`/dev/null`)，没有登陆`shell`(`/sbin/nologin`)，系统会更安全
`dirmessage_enable=YES`                       | 当切换到`FTP`服务器的某个目录时，是否显示该目录下的`.message`信息
`dirlist_enable=YES`                          | 是否启用通俗命令(如果设置为`NO`，那么只能使用`unix/linux`的命令)
`xferlog_enable=YES`                          | 是否启用上传和下载日志
`xferlog_std_format=YES`                      | 是否使用标准的`ftpd-xferlog`日志格式
`xferlog_file=/var/log/vsftpd.log`            | 将上传下载日志记录到`/var/log/vsftpd.log`中
`log_ftp_protocol=NO`                         | 当`xferlog_std_format`关闭且本选项开启时，记录所有`ftp`请求和回复，当调试时比较有用
`dual_log_enable=NO`                          | 如果启用，两个`LOG`文件会各自产生，默认的是`/var/log/xferlog`和`/var/log/vsftpd.log`
`vsftpd_log_file=/var/log/vsftpd.log`         | 这是被生成的`vsftpd`格式的`log`文件的名字(只有`xferlog_enable`被设置，而`xferlog_std_format`没有被设置时，此项才生效)
`syslog_enable=NO`                            | 如果启用，系统`log`将取代`vsftpd`的`log`输出到`/var/log/vsftpd.log`，`FTPD`的`log`工具将不工作
`connect_from_port_20=YES`                    | 是否启用`FTP`数据端口的连接请求
`listen=YES`                                  | 是否使用`standalone`启动`vsftpd`，而不是`super daemon(xinetd)`控制它(`vsftpd`推荐使用`standalone`方式)
`listen_ipv6=NO`                              | 与`listen`功能相同，但此项监听`IPV6`(两个只能设置一个)
`pam_service_name=vsftpd`                     | `PAM`认证服务配置文件名称，保存在`/etc/pam.d`目录下
`userlist_enable=YES`                         | 是否检查`userlist_file`设置文件
`tcp_wrappers=YES`                            | 是否使用`tcp_wrappers`作为主机访问控制方式(`tcp_wrappers`的两个配置文件`/etc/hosts.allow`允许访问的主机和`/etc/hosts.deny`拒绝访问的主机
`ftpd_banner=Welcome to yayi.biz FTP Service` | `FTP`欢迎信息(如果设置了`banner_file`，则此设置无效)
`banner_file=/etc/vsftpd/banner`              | 定义登录信息文件的位置
`check_shell=NO`                              | 是否检测`SHELL`
`chown_uploads=YES`                           | 是否开启匿名上传用户切换(如果开启，上传用户则变为`chown_username=daemon`指定的用户)
`chown_username=daemon`                       | 匿名上传文件的属主
`file_open_mode=0666`                         | 对于上传的文件设定权限
`idle_session_timeout=600`                    | 客户端超过`600s`没有动作则视为超时
`data_connection_timeout=300`                 | 数据传输时超过`300s`没有动作则视为超时
`connect_timeout=60`                          | 连接超时时间
`pasv_enable=YES`                             | 是否允许使用`PASV`模式
`pasv_promiscuous+NO`                         | 是否关闭`PASV`安全检查(删除`+NO`则开启)
`pasv_address=(none)`                         | 使`vsftpd`在`pasv`命令回复时跳转到指定的`IP`地址
`port_enable=YES`                             | 是否允许使用`PORT`模式
`prot_promiscuous`                            | 是否开启安全`PORT`检查(`+NO`则不开启)
`pasv_max_port=0`                             | 指定为被动模式数据连接分配的最大端口(`0`为任何)
`pasv_min_port=0`                             | 指定为被动模式数据连接分配的最小端口(`0`为任何)
`ACCEPT_TIMEOUT=60`                           | `PAVS`请求`60s`无响应则视为超时
`ascii_upload_enable=YES`                     | 是否可用`ASCII`模式上传(默认为`NO`)
`ascii_download_enable=YES`                   | 是否可用`ASCII`模式下载(默认为`NO`)
`secure_chroot_dir=/usr/share/empty`          | 这个选项必须指定一个空的数据夹，且任何登入者都不能有写入的权限，当`vsftpd`不需要`file system`的权限时，就会将使用者限制在此数据夹中，默认值为`/usr/share/empty`
`one_process_model=YES`                       | 是否使用单进程模式
`text_userdb_names=NO`                        | 是否可以查看文件拥有者的`UID`
`use_localtime=NO`                            | 显示目录清单时是用本地时间还是`GMT`时间，可以通过`mdtm`命令来达到一样的效果
`use_sendfile=YES`                            | 是否测试平台优化
`setproctitle_enable=YES`                     | 是否显示状态会话信息
`user_config_dir=/etc/vsftpd/userconf`        | 定义用户配置文件的目录
`local_root=xxx`                              | 定义本地用户登陆的根目录，注意定义根目录可以是相对路径也可以是绝对路径。相对路径是针对用户家目录来说的
`max_clients=0`                               | 可接受的最大`client`数目(`0`为不限制)
`max_per_ip=0`                                | 每个`ip`的最大`client`数目(`0`为不限制)
`connect_from_port_20=YES`                    | 是否启用`FTP`数据端口的数据连接
`ftp_data_port=20`                            | 设定`PORT`模式下的连接端口(只要`connect_from_port_20`被激活)
`listen_address=192.168.0.2`                  | 绑定`FTP`的`IP`地址(在多网卡或者多`IP`地址的机器上使用)
`listen_port=2121`                            | 绑定`FTP`使用使用端口
`ftp_data_port=2020`                          | 绑定`FTP`数据传输端口
`background=NO`                               | 启用时，`VSFTPD`将把监听进程置于后台；但访问`VSFTPD`时，控制台将立即被返回到`SHELL`
`force_dot_files=NO`                          | 如果激活，以`.`开始的文件和目录在目录列取的时候将会被显示，即使客户端没有使用`a`标识。这不包括`.`和`..`目录
`ssl_enable=NO`                               | 是否启用`SSL`
`force_local_data_ssl=YES`                    | 是否要求非匿名用户使用安全的`SSL`在数据线路上收发数据
`force_local_logins_ssl=YES`                  | 是否要求非匿名用户使用安全的`SSL`登录以发送密码
`ssl_tlsv1=YES`                               | 是否允许以`TLS V1`协议的连接，`TLS V1`连接将是首选
`ssl_sslv2=NO`                                | 是否允许以`SSL V2`协议的连接，`TLS V1`连接将是首选
`ssl_sslv3=NO`                                | 是否允许以`SSL V3`协议的连接，`TLS V1`连接将是首选
`session_support=NO`                          | 是否让`VSFTPD`去尝试管理登录会话

&emsp;&emsp;**补充说明**：
&emsp;&emsp;1. `anonymous_enable=NO`必须要打开，不能注释。
&emsp;&emsp;2. `ftp`服务器的根目录可以是多级目录，如`/home/fukangwei/ftp`。
&emsp;&emsp;3. 要设置好`ftp`服务器的根目录权限，否则上传文件时会出现`553 Could not create file`错误。

---

### tftp服务器

&emsp;&emsp;1. 安装`xinetd`、`tftp-hpa`和`tftpd-hpa`(`tftp-hpa`是客户端，`tftpd-hpa`是服务器端)：

``` bash
sudo apt-get install xinetd
sudo apt-get install tftp-hpa tftpd-hpa
```

&emsp;&emsp;2. 配置`TFTP`服务器：

``` bash
sudo vim /etc/default/tftpd-hpa
```

将原来的内容改为：

``` bash
TFTP_USERNAME="tftp"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_DIRECTORY="tftp根目录"  # 服务器目录，需要设置权限为777
TFTP_OPTIONS="-l -c -s"
```

&emsp;&emsp;3. 重新启动`TFTP`服务：

``` bash
sudo service tftpd-hpa restart
```


---

### ssh服务器

&emsp;&emsp;1. 安装`ssh-server`和`ssh-client`：

``` bash
sudo apt-get install openssh-server
sudo apt-get install openssh-client
```

&emsp;&emsp;2. 确认`sshserver`是否安装好：

``` bash
ps -e | grep sshd
```

如果看到`sshd`，那说明`ssh-server`已经启动了；如果只有`ssh-agent`，说明`ssh-server`还没有启动，需要执行如下命令：

``` bash
sudo /etc/init.d/ssh start
```

&emsp;&emsp;3. `SSH`默认服务端口为`22`，用户可以自定义成其他端口，需要修改的配置文件为`/etc/ssh/sshd_config`，把里面的`Port`参数修改成其他数组即可，然后重启`SSH`服务：

``` bash
sudo /etc/init.d/ssh restart
```


---

### VNC服务器

&emsp;&emsp;在`ubuntu`上安装并运行`VNC`服务器，推荐使用`tightvnc`：

``` bash
sudo apt-get update
sudo apt-get install tightvncserver
```

安装完成之后，使用如下命令运行`tightvnc`服务器：

``` bash
vncserver :1
```

第一次运行时需要你设置一个密码，该密码和系统用户密码无关。
&emsp;&emsp;在`Windows`上安装`vnc viewer`，下载地址为`http://www.realvnc.com/`。启动`vnc viewer`，在`VNC Server`上填入`VNC服务器IP地址:1`，点击`Connect`，输入`vnc`密码即可。

---

### sftp服务器

&emsp;&emsp;`sftp`服务器使用如下命令进行安装：

``` bash
sudo apt-get install openssh-server
```

&emsp;&emsp;`sftp`是`Secure File Transfer Protocol`的缩写，意思是安全文件传送协议，可以为传输文件提供一种安全的加密方法，与`ftp`有着几乎一样的语法和功能。`SFTP`本身没有单独的守护进程，它必须使用`sshd`守护进程(端口号默认是`22`)来完成相应的连接操作。所以从某种意义上来说，`SFTP`并不像一个服务器程序，而更像是一个客户端程序。`SFTP`使用加密传输认证信息和数据，所以是非常安全的。但由于这种传输方式使用了加密和解密技术，所以传输效率比普通的FTP要低得多。常用的命令如下：

``` bash
sftp user@host               # 登陆远程主机
lcd或lpwd                    # 针对本机的命令都加上“l”
put filename.txt directory   # 将本机文件上传到远程
mput *.*                     # 将当前文件夹下的文件上传到远程
get filename.file directory  # 下载远程文件到本地
mget *.* directory           # 下载目录下所有远程文件到本地
?                            # 帮助
bye、exit或quit              # 退出
```