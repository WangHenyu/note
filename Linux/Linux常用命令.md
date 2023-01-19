# Linux

<h4>PASSWORD</h4>

> root why0417
>
> userwhy 123

## 1.结构目录

> Liux中一切皆文件
>
> Linux以/为根目录

![image-20220818152035288](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220818152035288.png)

## 2.vim使用

<h4>三种模式</h4>

> 一般模式
>
> - 使用vim编辑文件时默认处于一般模式
>
> - 在此模式下，可以使用上、下、左、右键或者 k、j、h、l 命令进行光标移动
>
> - 可以对文件内容进行复制、粘贴、替换、删除等操作
>
> 编辑模式
>
> - 在编辑模式下可以对文件执行写操作
> - 进入编辑模式的方法是输入 i、a、o 等插入命令
> - 编写完成后按 Esc 键即可返回一般模式
>
> 命令模式
>
> - 如果要保存、查找或者替换一些内容等,就需要进入命令模式。
> - 命令模式的进入方法为：在编辑模式下按":" 键或 "/",Vim 窗口的左下方出现":"符号就进入命令模式了
> - 命令执行后会自动返回一般模式

<h4>模式切换</h4>

![image-20220820010214468](C:\Users\uuu\AppData\Roaming\Typora\typora-user-images\image-20220820010214468.png)

<h4>常用快捷键</h4>

<h5>一般模式</h5>

| 快捷键     | 描述           |
| ---------- | -------------- |
| yy         | 复制一行       |
| dd         | 删除一行       |
| p          | 粘贴           |
| u          | 撤销           |
| gg         | 光标移动到首行 |
| G          | 光标移到到尾行 |
| 20 shift+g | 光标移动到20行 |

<h5>命令模式</h5>

| 快捷键      | 描述       |
| ----------- | ---------- |
| /关键字     | 查找关键字 |
| :set nu     | 显示行号   |
| :set nonu   | 隐藏行号   |
| :%s/old/new | 替换内容   |
| :wq         | 保存并退出 |
| :q!         | 强制退出   |

## 3.常用命令

### 基本命令

1.关机命令: `halt` 或 `shutdown [可选项]` 或`poweroff`

- -h 关机
- -r 重启

> halt 关闭系统但不断电
>
> poweroff 关闭系统并且断电

2.重启命令: `reboot [可选项]`

- -f 强制重启

3.注销命令: `logout`

~~~shell
# 立刻关机
shutdown -h now
# 1分钟后重启
shutdown -r 1
~~~

提示：关机前最好同步数据

~~~shell
# 把内存数据同步到磁盘
sync
~~~

4.查看ip地址 `ip addr` `hostname -I`

5.显示当前目录 `pwd`

6.清理屏幕 `clear`

7.帮助命令语法

- `help [命令或配置文件]`

- `man  [命令或配置文件]`

~~~shell
# 查看cd命令
man cd
# 查看cd命令
help cd
~~~

<h5>区别</h5>

> help:用于显示shell内部的帮助信息,help命令只能显示shell内部的命令帮助信息
>
> man:man是在系统安装的时候安装了很多的文档 (man的帮助信息更详细)

8.切换目录 `cd [参数]`

​    .. 回到上级目录

​	-  回到上次访问目录

​	~  回到家目录(root)

~~~shell
# 绝对路径
cd /usr/tem
# 相对路径
cd usr/temp
# 回到家目录
cd 或 cd ~
~~~



### 文件管理

1.查看文件列表: `ls [可选项] [目录]` 或 `ll`

- -a 显示全部文件
- -l 显示文件属性,权限等

~~~shell
# 显示当前目录全部文件
ls -al
# 显示指定目录全部文件
ls -al /user/tem
~~~

2.创建目录 `mkdir [可选项] [目录名]`

- -p 创建多级目录

~~~shell
# 创建目录
mkdir dir
# 创建多级目录
mkdir -p dir/xxx/zzz
~~~

3.删除目录或文件 `rm [可选项] [目录名|文件名]`

- -r 递归删除目录中所有内容
- -f 强制删除且没有提醒
- -v 显示详细的执行过程

4.删除空目录 `rmdir [目录名]`

~~~shell
# 强制删除所有内容
rm -rf dir
# 强制删除文件
rm -ef file.text
# 删除空目录
rmdir dir
~~~

5.创建文件 `touch [文件名称]`

~~~shell
# 创建文件
touch file.txt
~~~

6.复制文件 `cp [可选项] [文件名|目录名] [目录路径]`

- -r 递归复制整个文件夹

~~~shell
# 复制文件或目录到指定文件夹
cp file.txt /usr/dir
# 递归复制全部文件
cd -r xxxdir /usr/dir
~~~

6.文件重命名 `mv [旧文件名] [新文件名]`

7.移动文件 `mv [目标文件|目录名] [目标目录]`

~~~shell
# 重命名文件(相同目录)
vm file.txt file1.txt
# 移动文件(不同目录)
vm file.txt /usr/dir
# 移动文件夹
vm xxxdir /usr/dir
~~~

8.查看文件信息 `cat [可选项] [文件名]`

- -n 显示行号

  查看文件头10行 `head [文件名]`

  查看文件尾10行 `tail [文件名]`

~~~shell
# 查看文件并显示行号
cat -n file.txt
# 查看文件头5行
head -n 5 file.txt
# 查看文件尾5行
tail -n 5 file.txt
~~~

9.more命令查看文件 `more [文件名]`

 ~~~shell
# 查看文件
more file.txt
 ~~~

| 操作   | 功能说明     |
| ------ | ------------ |
| q      | 离开move     |
| 空格   | 下翻一页     |
| Enter  | 下翻一行     |
| Ctrl+F | 向下滚动一屏 |
| Ctrl+B | 返回上一屏   |
| =      | 输出当前行号 |

10.less命令 `less [文件名]`

~~~powershell
# 查看文件
less file.txt
~~~

| 操作     | 功能说明            |
| -------- | ------------------- |
| 空格     | 下翻一页            |
| pagedown | 下一页              |
| pageup   | 上一页              |
| /字符串  | n向下查找 N向上查找 |
| ?字符串  | n向上查找 N向下查找 |
| q        | 退出                |



### 用户管理

1.添加用户 `useradd [可选项] [用户名]`

- -d 在哪个目录下创建用户
- -g 添加用户到某个组

~~~shell
# 添加用户(默认在/home中)
useradd Alice
~~~

2.修改密码 `passwd [用户名]`

3.删除用户 `userdel [可选项] [用户名]`

- -r 删除用户及用户主目录

~~~shell
# 删除目录但保留用户主目录
userdel Alice
~~~

4.用户其他

- 显示当前用户所在目录 `pwd`

- 查看用户信息 `id [用户名]`

- 切换用户 `su [用户名]`

- 返回原来的用户或注销 `logout`

- 查看用户信息`whoami` 或 `who am i`

> whoami只显示用户名,who am i显示登录用户的用户名以及登陆时间

5.用户组

- 新建用户组 `groupadd [组名]`
- 删除用户组 `groupdel [组名]`
- 修改组名  `groupmod -n [新组名] [旧组名]`
- 添加用户到指定组 `useradd -g [组名] [用户名]`
- 移动用户到指定组 `usermod -g [组名] [用户名]`

> 注意: 删除组前需要将组内的用户删除或移走

~~~shell
# 新建用户组
groupadd groupA
# 向groupA中新建用户
useradd -g groupA Bob
# 将用户移动到groupA组
usermod -g groupA Alice
# 修改组名
groupmod -n groupB groupA
# 删除用户组
groupdel groupB
~~~

<h4>sudo</h4>

> 设置普通用户具有root权限
>
> ​	1.使用root用户操作 `su root` 已经是root用户可跳过此步
>
> ​	2.修改配置文件 `vi /etc/sudoers`
>
> ​	3.填写下面内容
>
> ​	4.强制修改:wq!

~~~shell
 ## Allow root to run any commands anywhere
 root    ALL=(ALL)       ALL
 wanghy  ALL=(ALL)       ALL
 # 采用 sudo 命令时不需要输入密
 wanghy  ALL=(ALL)       NOPASSWD:ALL
~~~

这样普通用户也能使用root权限

~~~shell
# 使用root权限创建文件夹
sudo mkdir module
~~~



### 文件属性

~~~shell
# 文件属性
drwxr-xr-x.  2 root root 4096 8月  18 23:19 dir
-rwxr-xr-x.  1 root root 8400 4月  14 12:35 hello
-rw-r--r--.  1 root root   59 4月  14 12:35 hello.c
-rw-r--r--.  1 root root 1485 5月  22 14:14 hello.lua
~~~

| 文件类型    | 属主权限               | 属组权限               | 其他用户权限           |
| ----------- | ---------------------- | ---------------------- | ---------------------- |
| d           | r        w        x    | r        w        x    | r        w        x    |
| d目录 -文件 | 读       写       执行 | 读       写       执行 | 读       写       执行 |

<h5>修改文件属性</h5>

1.`chgrp [可选项]  [用户组] [文件名|目录]` 更改文件属组

2.`chown [可选项]  [属主名] [文件|目录页]` 更改文件属主

3.`chmod [可选项] [rwx] [文件|目录]` 修改文件属性

- -R 递归操作
- r：4
- w：2
- x：1

~~~shell
# 修改文件属组
chgrp root file.txt 
# 修改目录所有者
chown -R why /user/dir
# 修改文件的所有者和所属组
chown kz:kz /user/txt
# 可读可写可执行
chmod 777 file.txt
~~~



### 查找命令

1.find命令 `find [搜索范围] [可选项] [文件名]`

- -name 按文件名字查找
- -user 按文件拥有者查找
- -size 按文件大小查找

~~~shell
# 查找home目录下的hello.txt文件
find /home -name hello.txt
# 查找home目录下用户名是root的文件
find /home -user root
# -n小于、+n大于、=n等于
find /home -size +20M
~~~

2.locate命令 `locate [文件|目录]`

> locate命令用来查找文件或目录
>
> locate命令要比find-name快得多
>
> 原因在于它不搜索具体目录,而是搜索一个数据库/var/lib/mlocate/mlocate.db 这个数据库中含有本地所有文件信息
>
> Linux系统自动创建这个数据库,并且每天自动更新一次
>
> 因此 我们在用whereis和locate查找文件时有时会找到已经被删除的数据或者刚刚建立文件却无法查找到,原因就是因为数据库文件没有被更新
>
> 为了避免这种情况,可以在使用locate之前先使用updatedb命令手动更新数据库。

~~~shell
# 更新文件数据库
updatedb
# 查找hello.txt文件的位置 
locate hello.txt
~~~

3.grep命令 `grep [可选项] [查找内容] [源文件]`

- -n 显示匹配行及行号
- -i 忽略字母大小写

> grep作用：过滤查找

~~~shell
# 查找"hello"所在行并显示行号
grep -n "hello" hello.txt
~~~

4.管道符 `|`

> 管道符作用: 将前一个命令的处理结果输出传递给后面的命令处理
>
> 管道符一般配合grep使用

~~~shell
# 查找"hello"所在行
cat hello.txt | grep "hello"
# 显示redis相关进程
ps -ef | grep redis
~~~

### 压缩解压

1.zip压缩 `zip [可选项] xxx.zip [文件|目录]`

- -r 压缩目录

2.unzip解压 `unzip [可选项] xxx.zip`

- -d 指定解压后文件的位置

~~~shell
# 压缩文件
zip hello.zip hello.txt
# 压缩目录
zip -r dir.zip dir
# 解压文件到当前目录
unzip hello.zip
# 解压文件到指定目录
unzip -d /usr/dir hello.zip
~~~

3.gzip压缩 `gzip [文件]`

4.gunzip压缩 `gunzip xxx.gz`

> 只能将文件压缩为*.gz 文件
>
> 只能压缩文件不能压缩目录 
>
> 不保留原来的文件 

~~~shell
# 压缩
gzip hello.txt
# 解压
gunzip hello.gz
~~~

5.tar打包 `tar [可选项] xxx.tar.gz [打包内容]`

- -z 打包同时压缩
- -x 解压缩.tar文件
- -c 生成.tar打包文件
- -v 显示详细信息
- -f 指定压缩后的文件名
- -C 解压到指定文件夹

~~~shell
# 将两个文件压缩成pc.tar.gz
tar -zcvf pc.tar.gz pig.txt dog.txt
# 解压到当前文件夹
tar -zxvf pc.tar.gz
# 解压到指定文件夹
tar -zxvf pc.tar.gz -C /usr/dir
~~~



### 磁盘管理

1.df命令 `df [可选项]`

- -h 以M为单位显示
- -a 不仅查看子目录大小还要包括文件 

> df作用:列出文件系统整体的磁盘使用量

~~~shell
# 磁盘使用量
df -a
# 磁盘使用量
df -h
~~~



2.du命令 `du [可选项]`

- -a 不仅查看子目录大小还要包括文件 
- -s 只显示总和
- -m 检查此目录下每个目录所占容量
- -c 显示所有的文件和子目录大小后显示总和

~~~shell
du -c
du -sm
~~~

3.查看设备挂载情况 `lsblk [可选项]` 

- -f 查看详细的设备挂载情况

4.查看分区详情 `fdisk [可选项]` 

- -l 显示所有硬盘的分区列表

> 该命令必须在 root 用户下才能使



### 进程管理

1.查看哪个端口的进程 sudo lsof -i:8080

1.查看进程信息 `ps [可选项]`

- a 列出带有终端的所有用户的进程 
- x 列出当前用户的所有进程包括没有终端的进程 
- u 面向用户友好的显示风格
- –e 显示所有进程信息
- –f 以全格式显示进程信息

> 如果想查看进程的 CPU 占用率和内存占用率可以使用 aux
>
> 如果想查看进程的父进程 ID 可以使用 ef

~~~shell
# 查看系统中所有进程
ps axu
# 可以查看子父进程之间的关系
ps -ef
~~~

~~~shell
ps axu
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          4  0.0  0.0      0     0 ?        S<   19:18   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        S    19:18   0:00 [ksoftirqd/0]
root          7  0.0  0.0      0     0 ?        S    19:18   0:00 [migration/0]
root          8  0.0  0.0      0     0 ?        S    19:18   0:00 [rcu_bh]

ps -ef
UID         PID   PPID  C STIME TTY      TIME     CMD
root          4      2  0 19:18 ?        00:00:00 [kworker/0:0H]
root          6      2  0 19:18 ?        00:00:00 [ksoftirqd/0]
root          7      2  0 19:18 ?        00:00:00 [migration/0]
root          8      2  0 19:18 ?        00:00:00 [rcu_bh]

~~~

| USER     | PID    | %CPU                | %MEM                     | VSZ                    | RSS                    | STAT         | START    | TIME                | COMMAND            |
| -------- | ------ | ------------------- | ------------------------ | ---------------------- | ---------------------- | ------------ | -------- | ------------------- | ------------------ |
| 用户名称 | 进程号 | 进程占用CPU的百分比 | 进程占用物理内存的百分比 | 进程占用的虚拟内存大小 | 进程占用的物理内存大小 | 终端名称缩写 | 进程状态 | 进程使用CPU的总时间 | 产生此进程的命令名 |

> 常见进程状态
>
> R：运行状态、S：睡眠状态、T：暂停状态、 Z：僵尸状态、s：包含子进程、l：多线程、+：前台显示

| UID     | PID     | PPID      | C                           | STIME          | TTY            | TIME    | CMD                      |
| ------- | ------- | --------- | --------------------------- | -------------- | -------------- | ------- | ------------------------ |
| 用户 ID | 进程 ID | 父进程 ID | CPU用于计算执行优先级的因子 | 进程启动的时间 | 完整的终端名称 | CPU时间 | 启动进程所用的命令和参数 |

2.终止进程 `kill [可选项] [进程ID] `或 `killall [进程名]`

- -9 强制进程立即停止
- -15 停止进程
- -KILL 终止进程
- -STOP 暂停进程
- -CONT 恢复进程

~~~shell
# 强制停止进程
kill -9 8271
# 通过进程名杀死进程
killall firefox
~~~

3.查看进程树 `pstree [可选项]`

- -p 显示进程的PID
- -u 显示进程所属用户

~~~shell
pstree -p
pstree -u
pstree -up
~~~

4.实时监控系统进程状态 `top [可选项]`

- -d 秒数 指定top命令每隔几秒更新(默认是3秒)

- -i 使top不显示任何闲置或者僵死进程

- -p 通过指定监控进程ID来仅仅监控某个进程的状态

~~~shell
top -d 10
top -i
top -p
~~~

top命令的交互模式当中可以执行命令

| 操作 | 功能                   |
| ---- | ---------------------- |
| P    | 默认项,以CPU使用率排序 |
| M    | 以内存使用率排序       |
| N    | 以PID排序              |
| q    | 退出top                |

~~~shell
# 任务队列信息
top - 22:36:19 up  3:17,  1 user,  load average: 0.00, 0.01, 0.05
# 进程信息
Tasks: 105 total,   2 running, 103 sleeping,   0 stopped,   0 zombie
# CPU信息
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
# 物理内存信息
KiB Mem :   995676 total,    77784 free,   237008 used,   680884 buff/cache
# 交换分区信息
KiB Swap:  2097148 total,  2096884 free,      264 used.   566524 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
   101 root      20   0       0      0      0 S  0.3  0.0   0:12.87 kworker/0:3
   580 root      20   0  295564   5256   4028 R  0.3  0.5   0:19.65 vmtoolsd
  7019 root      20   0  162088   2220   1548 R  0.3  0.2   0:00.05 top
     1 root      20   0  128432   6484   3592 S  0.0  0.7   0:02.93 systemd
     2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd
~~~

5.显示网络状态和端口占用信息 `netstat [可选项]`

- -a 显示所有正在监听和未监听的套接字

- -n 拒绝显示别名，能显示数字的全部转化成数字 

- -l 仅列出在监听的服务状态 

- -p 表示显示哪个进程在调用

~~~shell
# 查看该进程网络信息
netstat -anp | grep 进程号
# 查看网络端口号占用情况
netstat -nlp | grep 端口号
~~~



### 服务相关

> 查看服务：
>
> ​	1.进入目录/usr/lib/systemd/system
>
> ​	2.查看服务列表 ls -al

1.启动服务 `systemctl start [服务名]`

2.关闭服务 `systemctl stop [服务名]`

3.重启服务 `systemctl restart [服务名]`

4.查看服务状态 `systemctl status [服务名]`

5.查看服务开机启动状态 `systemctl list-unit-files`

6.关掉指定服务的自动启动 `systemctl disable [服务名]`

7.开启指定服务的自动启动 `systemctl enable [服务名]`



### 防火墙

1.查看防火墙状态 `systemctl status firewalld`

2.关闭防火墙 `systemctl stop firewalld`

3.开启防火墙 `systemctl start firewalld`

4.设置防火墙开机自启 `systemctl enable firewalld.service`

5.关闭防火墙开启自启 `systemctl disable firewalld.service`

6.查看防火墙端口 `firewall-cmd --list-all`

7.开放防火墙80端口 `firewall-cmd --add-port=80/tcp --permanent`

8.重启防火墙 `firewalld-cmd --reload`



### 运行级别

Linux系统七大运行级别

> 0 关机
>
> 1 单用户(找回丢失密码)
>
> 2 多用户状态无网络服务
>
> 3 多用户状态有网络服务(常用)
>
> 4 系统未使用保留给用户
>
> 5 图形界面
>
> 6 系统重启

切换运行级别 `init [运行级别]`

查看运行级别 `runlevel` 或 `systemctl get-default`

设置默认运行级别 `systemctl set-default [运行级别]`

~~~shell
# 切换运行级别
init 5
# 设置默认运行级别
systemctl set-default multi-user.target
# multi-user.target 3级别
# graphical.target  5级别
~~~



### 其他命令

1.echo命令 `echo [可选项] [输出内容 ]`

- -e 支持\控制字符转移

~~~shell
# 输出内容到控制台
echo "hello linux"
# 支持转义
echo -e "hello \n linux"
~~~

<h5>输出重定向和追加</h5>

> 输出重定向 `>`
>
> 追加 `>>`

~~~shell
# 写入内容到指定文件
echo "hello" > /user/hello.txt
# 将文件1中的内容追加到文件2
cat file1.txt >> file2.txt
~~~



2.ln命令 `ln [可选项] [文件|目录] [连接名]`

- -s 软链接（类似于快捷方式）

~~~shell
# 创建硬链接
ln hello.c hel.c
# 创建软链接
ln -s hello.c hel.c
# 删除链接
rm -rf hel.c
~~~

<h5>硬链接和软链接的区别</h5>

软链接：这个链接文件包含的是文件地址,源文件被删除则链接失效

硬链接：相当于是文件的别名,通过多个文件名操作同一个文件



3.时间日期类

  显示当月日历 `cal`

  日期命令 `date [可选项]`

​	-s  设置日期

​	-d  显示非当前时间

​	+%Y 显示年份

​	+%m 显示月份

​	+%d 显示哪天

~~~shell
# 显示2022年的日历
cal 2022
# 按格式显示当前时间
date "+%Y-%m-%d %H:%M:%S
# 设置日期
date -s "2022-08-17 23:49:23"
# 显示前1天的日期
date -d "1 days ago"
~~~



4.history命令 `history [可选项]`

~~~shell
# 查看最近执行的10条命令
history 10
# 执行曾经执行过的第20条命令
!20
~~~



## 4.软件包管理

### RPM

> RPM（RedHat Package Manager）RedHat软件包管理工具,类似windows里面的setup.exe是Linux这系列操作系统里面的打包安装工具

<h4>RPM包的名称格式 </h4>

> Apache-1.3.23-11.i386.rpm 
>
> - apache 软件名称 
> - 1.3.23-11 软件的版本号，主版本和此版本 
> - i386 是软件所运行的硬件平台，Intel 32位处理器的统称
> - rpm 文件扩展名，代表RPM包

<h4>RPM命令</h4>

1.查询所安装的rpm软件包 `rpm -qa`

- -q query
- -a all

~~~shell
# 查询所有
rpm -qa
# 按软件包名查询
rpm -qa | grep firefox
~~~

2.安装 `rpm -i [RPM全包名]`

- -i install 安装
- -v --verbose 显示详细信息
- -h --hash 显示进度条
- --nodeps 安装前不检查依赖

~~~shell
rpm -ivh firefox-68.10.0-1.el7.centos.x86_64
~~~

3.卸载 `rpm -e [软件包]`

- -e 卸载软件包
- --nodeps 卸载时不检查依赖,这会导致使用该软件包的软件不能正常工作

~~~shell
rpm -e firefox
~~~



### YUM

> YUM（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器
>
> 基于RPM包管理,能够从指定的服务器自动下载RPM包 并且安装
>
> 可以自动处理依赖性关系,并且一次安装所有依赖的软件包,无须繁琐地下载、安装
>
> YUM类似Java中的Maven,可以从镜像网站上下载程序并直接安装

<h4>YUM命令</h4>

基本语法 `yum [可选项] [参数] [npm包]`

- -y 对所有问题都回答yes

参数说明

| 参数         | 功能                          |
| ------------ | ----------------------------- |
| install      | 安装rpm软件包                 |
| update       | 更新rpm软件包                 |
| check-update | 检查是否有可用的更新rpm软件包 |
| remove       | 删除指定的rpm软件包           |
| list         | 显示软件包信息                |
| clean        | 清理yum过期的缓存             |
| deplist      | 显示yum软件包的所有依赖关系   |

~~~shell
# 安装
yum -y install firefox
# 卸载
yum -y remove firefox
# 检测更新
yum -y check-update firefox
# ....
~~~



<h4>修改YUM源</h4>

> 默认的系统YUM源需要连接国外apache网站,网速比较慢
>
> 可以修改关联的网络YUM源为国内镜像的网站

1.安装wget

~~~shell
# wget用来指定的URL下载文件
yum install wget
~~~

2.备份默认的repos文件

~~~shell
# 进入目录
cd /etc/yum.repos.d
# 备份
cp CentOS-Base.repo CentOS-Base.repo.bak
~~~

3.下载aliyun的repos文件

~~~shell
# 阿里云
wget http://mirrors.aliyun.com/repo/Centos-7.repo
# 网易163
wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
~~~

4.替换默认的repos

~~~shell
# 替换
mv Centos-7.repo CentOS-Base.repo
~~~

5.清理旧缓存数据,缓存新数据

~~~shell
# 清理旧缓存数据
yum clean all
# 把服务器的包信息下载到本地电脑缓存起来
yum makecache
~~~

6.使用



## 5.网络配置

<h4>设置静态Ip地址</h4>

> 由于虚拟机上的LinuxIp地址是动态分配的
>
> 每次重启虚拟机分配的IP不固定,使用远程工具链接不太方便

1.打开网卡配置文件

~~~shell
# 注: ifcfg-ens33文件在不同版本的Linux中名字可能不同
vim /etc/sysconfig/network-scripts/ifcfg-ens33
~~~

2.查看子网IP

> VMware &rarr; 查看 &rarr; 虚拟网络编辑器 &rarr; NET模式 &rarr; 子网IP和子网掩码
>
> 下面设置的静态IP、网关地址、DNS服务器前缀要和子网IP相同
>
> 我的是192.168.140.0

2.修改配置文件

~~~shell
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="static"         #使用静态IP
IPADDR="192.168.140.100"   #设置静态IP地址
NETMASK="255.255.255.0"    #子网掩码
GATEWAY="192.168.140.2"    #网关地址
DNS1="192.168.140.2"       #DNS服务器
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="520a23d6-3982-41fb-960a-3bbf506a25dd"
DEVICE="ens33"
ONBOOT="yes"
~~~

3.保存后重启服务

~~~shell
systemctl restart network
~~~

<h5>问题解决</h5>

`Failed to start LSB: Bring up/down`错误解决方法

~~~shell
systemctl stop NetworkManager
systemctl disable NetworkManager
~~~







