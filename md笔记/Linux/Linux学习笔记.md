@[TOC](目录:)

# 1、了解Linux

## 1.1介绍

特点：免费、开源、安全、高效、稳定、高并发

创始人：Linus Torvalds

吉祥物：企鹅、tux

## 1.2主要发行版本

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210625215511.png)



# 2、Linux目录结构

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210625215633.png)

具体介绍：

- /bin：Binary，存放最常用的命令
- /sbin：s就是`super user`的意思，存放系统管理员使用的系统管理程序
- /home：普通用户的主目录，每个用户都有一个自己权限的主目录`~`
- /root：root用户的主目录
- /boot：启动Linux启动时使用的一些核心文件，包括连接文件和镜像文件。
- /proc：虚拟的目录，系统文件的映射，访问这个目录可以获取系统信息
- /srv：service，服务启动之后需要提取的数据
- /sys：安装了linux2.6内核新出现的一个文件系统
- /tmp：临时文件
- /dev：设备管理器，所有硬件以文件的形式存储
- /media：自动识别的设备：光驱、U盘，挂载到这里
- /opt：额外安装软件的地方
- /usr/local：另一个安装软件的地方，一般是通过编译源码的方式安装的程序
- /var：不段被扩充的东西，经常被修改的东西，如日志文件
- /selinux[security-enhanced linux]360：安全子系统，可以控制程序只访问特定的文件



# 3、vi和vim编辑器

## 3.1是什么？

所有Linux都有内建的vi文本编辑器

vim具有程序编辑的功能，可以看成是vi的增强版本。

## 3.2三种模式

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210625221834.png)

## 3.3快捷键（正常模式下）

1. 拷贝：
   - 拷贝当前行：yy
   - 拷贝当前行向下5行：5yy
2. 删除：
   - 删除当前行：dd
   - 删除当前行向下5行：5dd
3. 查找某个单词：
   - /关键字，回车查找，n就查找下一个
4. 行号：
   - 设置文件行号：set nu
   - 取消文件行号：set nonu
5. 跳转：
   - 跳到末行：G
   - 跳到首行：gg
   - 跳到第20行：20 shift+g
6. 撤销：u

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210625222759.png)

# 4、开机重启、登录注销

## 4.1关机重启

```
shutdown
	shutdown -h now 立刻关机
	shutdown -h 1 1分钟后关机
	shutdown -r now 立刻重启
halt 关机
reboot 重启系统
sync 把内存的数据刷新到磁盘
```

**注意：**

关机或重启前，都必须要执行：syn

## 4.2登录注销

```
su -用户名 切换用户
例如：
su -root
su -username

logout 注销用户
	在图形运行级别没用，在运行级别3下才有用
```

# 5、用户管理

## 5.1基本概念

用户至少属于一个组，默认为与用户名同名的一个组。

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210625223728.png)

## 5.2添加用户

```
useradd [选项] 用户名

-d 指定目录
	useradd -d 指定的家目录 用户名
-g 直接加上组
	useradd -g 用户组 用户名
```



## 5.3修改用户密码

```
passwd 用户名
```



## 5.4删除用户

```
userdel [选项] 用户名

-r 同时删除家目录
	userdel -r username
```

一般删除用户时，都不会删除家目录，因为肯有一些重要文件。



## 5.5查询用户信息

```
id 用户名
```



## 5.6切换用户

```
su -用户名

exit 返回到原来的用户
```

高权限用户切换到低权限用户：不需要输入密码

反之：需要输入密码



# 6、用户组管理

## 6.1增加组

```
groupadd 组名
```



## 6.2删除组

```
groupdel 组名
```



## 6.3修改用户的组

```
usermod -g 用户组 用户名
```



## 6.4几个配置文件

- /etc/passwd文件：用户的配置文件，记录用户信息
- /etc/shadow文件：口令的配置文件
- /etc/group文件：组的配置文件



# 7、实用指令

## 7.1运行级别

- 0：关机
- 1：单用户，用于找回密码
- 2：多用户状态，没有网络服务
- 3：多用户状态，有网络服务
- 4：系统未使用，留给用户
- 5：图形化界面
- 6：系统重启

更改默认启动的运行级别：

/etc/inittab 的id​ : 5 : initdefault

## 7.2切换运行级别

```
init [0123456]

init 3
init 0
```

## 7.3找回密码

1. 开机
2. 引导时输入：回车（这个操作的前提是：主机就在你身边）
3. 输入：e
4. 选择第二行：编辑内核
5. 输入：e
6. 输入：1
7. 输入：回车
8. 再输入：b，这样就进入了单用户模式
9. 使用passwd指令修改root用户密码

## 7.4帮助指令

```
man [命令或配置文件] 获得帮助信息

man ls
```

```
help [命令] 获得shell内置命令的帮助信息

help cd
```



## 7.5文件目录指令

```
pwd 显示当前工作目录的绝对路径

cd 切换目录
```



```
ls [选项] [目录或文件]

-a 显示当前目录所有文件和目录，包括隐藏的
-l 以列表的方式显示详细信息
```

### 7.5.1文件夹

#### 创建文件夹

```
mkdir [选项] 要创建的目录

-p 递归创建多级目录
```

#### 删除文件夹

```
redir [选项] 要删除的空目录

-rf 删除非空的目录
```



### 7.5.2文件

#### 创建文件

```
touch 文件名称1 文件名称2 、、、
```

#### 复制文件

```
cp [选项] 源文件或目录 目标目录

-r 递归复制整个文件夹
```

#### 删除文件

```
rm [选项] 要删除的文件或目录

-r 递归删除
-f 强制删除不提示
```

#### 移动文件

```
mv 源文件或目录 目标目录
```



### 7.5.3查看文件内容

#### cat

只读打开，一次全部显示

```
cat [选项] 文件名

-n 显示行号
```

#### more

以全屏幕的方式按页显示文件内容，内置许多快捷键

```
more 文件名
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626144949.png)

#### less

分屏的方式显示，类似more，但比more更强大，支持各种终端

不是一次全部加载，适用于大型文件。

```
less 文件名
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626145156.png)

#### head

查看文件开头部分内容，默认显示前10行

```
head [选项] 文件名

-n 5 显示前5行
```

#### tail

查看文件结尾部分内容，默认显示后10行

```
tail [选项] 文件名

-n 5 显示后5行
-f 实时追踪文件的更新
```



### 7.5.4重定向

```
> 覆盖原来文件的内容

>> 不覆盖，追加到文件的尾部

ls -l > a.txt
ls -l >> b.txt
```



### 7.5.5echo

```
echo [选项] [输出内容] 显示内容到控制台
```



### 7.5.6创建链接文件

```
ln [选项] 链接目标文件或目录 链接名

-s 
```



### 7.5.7查看历史命令

```
history 查看所有历史命令

history 10 查看最近的10个命令

!10 执行历史编号的10的命令
```



## 7.6时间日期

### 7.6.1显示当前日期

```
date 显示当前时间

date+%Y 显示当前年份
date+%m 显示当前月份
date+%d 显示当前日期
date+%Y-%m-%d %H:%M:%S 显示年月日时分秒
```

### 7.6.2设置时间

```
date -s 字符串时间

date -s 2022-10-10 11:12:33
```

### 7.6.3日历

```
cal [选项]

cal 显示当月的日历
cal 2022 显示2022年的日历
```



## 7.7文件目录搜索查找

### find

递归查找各个子目录，查找到文件或目录显示到终端

```
find [搜索范围] [选项]

-name 按文件名
-user 按用户
-size 按文件大小{+- 20Mk}
```

### locate

利用事先建立的系统文件数据库进行查找，快速定位，无需便利整个文件系统。

使用前必须使用`updatedb`创建文件数据库

```
locate 文件名
```



## 7.8过滤和管道

### 过滤

```
grep [选项] 查找内容 目标文件

-n 显示匹配行和行号
-i 忽略字母大小写
```

### 管道

```
前指令 | 后指令
	将前指令的执行结果传输给后指令，让后指令继续执行 
```



## 7.9压缩和解压缩

### gzip/gunip

压缩之后不会保留原来的文件

```
gzip 文件名 压缩文件成*.gz文件
gunzip 文件名.gz 解压成原文件
```

### zip/unzip

```
zip [选项] xxx.zip 文件名

-r 递归压缩，即压缩目录

unzip [选项] xxx.zip

-d<目录> 指定解压后文件的存放目录
```

### tar

打包指令，打包后是`.tar.gz`文件

```
tar [选项] xxx.tar.gz 文件目录名

-c 产生.tar打包文件
-v 显示详细信息
-f 指定压缩后的文件名
-z 打包同时压缩
-x 解包.tar文件

常用压缩
tar -zcvf a.tar.gz a1.txt a2.txt

常用解压
tar -zxvf a.tar.gz
```



# 8、权限管理

## 8.1组介绍

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626202600.png)



## 8.2文件目录的所有者

### 查看所有者

```
ls -ahl
```

### 修改所有者

```
chown 用户名 文件名
```



## 8.3组管理

### 组的创建

```
groupadd 组名
```

### 修改文件所在的组

```
chgrp 组名 文件名
```

### 改变用户所在的组

```
usermod -g 组名 用户名

usermod -d 目录名 用户名 
	改变该用户登录的初始目录
```



## 8.5权限介绍

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626203420.png)

- r：可读
- w：可写
- x：可执行

## 8.6修改权限

```
chmod 

chmod u=rwx,g=rx,0=x 文件名
	u所有者 g所在组 o其他人 a所有人
chmod o+w 文件名
chmod a-x 文件名

chmod 751 文件名
	r=4 w=2 x=1
```

## 8.7修改文件所有者

```
chown 新所有者名 文件名

chown 新所有者:新组名 文件名

-R 如果是目录，则递归修改
```



# 9、crond任务调度

## 9.1原理

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626204410.png)

## 9.2基本语法

```
crontab [选项]

-e 编辑crontab定时任务
-l 查询crontab任务
-r 删除当前用户的所有crontab任务

service crond restart
	重启任务调度
```

## 9.3使用

1. 设置任务调度文件：/etc/crontab
2. 设置个人任务：crontab -e
3. 输入任务：`*/1****ls -l /etc/>/tmp/to.txt`
4. 这样就会定时触发调度任务

**参数说明：**

| 项目   | 含义                 | 范围                    |
| ------ | -------------------- | ----------------------- |
| 第1个* | 一小时当中的第几分钟 | 0-59                    |
| 第2个* | 一天当中的第几个小时 | 0-23                    |
| 第3个* | 一个月当中的第几天   | 1-31                    |
| 第4个* | 一年当中的第几月     | 1-12                    |
| 第5个* | 一周当中的星期几     | 0-7（0和7都代表星期日） |

**特殊符号说明：**

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626205451.png)

**常用案例：**

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626205515.png)



# 10、磁盘分区与挂载

## 10.1分区的方式

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626205749.png)

## 10.2windows下的磁盘分区

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626205823.png)

## 10.3Linux分区介绍

### 原理：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626210007.png)

### 磁盘说明：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626210038.png)

## 10.4查看分区情况

```
lsblk -f
```

## 10.5如何增加一快硬盘

1. 物理上添加硬盘
2. 分区：fdisk /dev/sdb
3. 格式化：mkfs -t ext4 /dev/sdb1
4. 挂载：
   - 创建一个：/home/newdisk
   - 挂载：mount /dev/sdb1 /home/newdisk
5. 设置自动挂载：vim /etc/fstab
   - ![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626210532.png)

## 10.6磁盘情况查询

### 系统整体情况

```
df -lh
```

### 指定目录磁盘

```
du [选项] 目录名
	默认为当前目录

-h 带计量单位
-s 指定目录占用大小汇总
-a 含文件
-c 列出明细的同时，增加汇总值
--max-depth=1 子目录深度

du -ach --max-depth=1 /opt
```

## 10.7常用指令

### 统计文件夹下的文件个数：

```
ls -l /home | grep ^_ | wc -l
```

### 统计文件夹下的目录个数：

```
ls -l /home | grep ^d | wc -l
```

### 统计文件夹下的文件个数，包括子文件夹的：

```
ls -lR /home | grep ^_ | wc -l
```

### 以树状显示目录结构：

1. 安装工具：yum install tree

2. ```
   tree
   ```



# 11、网络配置

## 11.1原理

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210626211816.png)



# 12、进程管理

## 12.1基本介绍

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627135837.png)

## 12.2显示系统执行的进程

```
ps [选项]

-a 显示当前终端所有的进程
-u 以用户的格式显示进程信息
-x 显示后台进程运行的参数
-e 显示所有的进程
-f 全格式

常用
ps -aux | grep xxx 查看特定的xxx进程
ps -ef 查看所有的父进程
```

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627140043.png)

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627140118.png)

详解：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627140201.png)

## 12.3终止进程

```
kill [选项] 进程号

-9 强制杀死进程

killall 进程名称
	杀死所有进程，支持通配符
```

## 12.4查看进程树

以树的形式显示进程

```
pstree [选项]

-p 显示进行的pid
-u 显示进程所属的用户
```

## 12.5服务管理

介绍：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627140832.png)

```
service 服务名 [start|stop|restart|reload|status]

CentOS7.0之后，不再使用service，而是使用systemctl
```

## 12.6开机流程

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627141145.png)

## 12.7设置服务再各个运行级别的自启动/关闭

```
chkconfig --list 查看所有服务
chkconfig 服务名 --list 查看某个服务

chkconfig --level 5 服务名 on/off
	设置某个服务在运行级别5自启动/关闭
```

**注意：**设置之后需要重启机器`reboot`才能生效。

## 12.8动态监控进程

### 介绍：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627141740.png)

### 语法：

```
top [选项]

-d 秒数 指定每个多少秒更新，默认3秒
-i 使top不显示任何闲置或僵死进程
-p 指定监控的进程id
```

交互操作说明：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627142012.png)

## 12.9查看系统网络情况

```
netstat [选项]

-an 按一定顺序排列输出
-p 显示哪个进程在调用
```



# 13、软件安装卸载

## 13.1 rpm

### 介绍：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627142343.png)

### 查询：

```
rpm [选项]

-qa 查询所有安装的rpm软件包
-q 软件名 
	查询指定的软件包
-qi 软件包名
	查询指定的软件包信息
-ql 软件包名
	查询软件包中的文件安装到哪里了
-qf 文件名
	查询某个文件属于哪个软件包
```

### 卸载：

```
rpm -e 软件包名称
	如果有其他软件包引用这个软件包，就会产生错误信息
rpm -e --nodeps 软件包名称
	强制卸载删除
```

### 安装：

```
rpm -ivh 软件包全路径名称

-i 安装
-v 提示
-h 进度条
```

## 13.2 yum

### 介绍：

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627161050.png)

![](https://azhu12138.oss-cn-shenzhen.aliyuncs.com/img/20210627161109.png)

### 基本指令：

```
yum list 查询服务器全部软件列表

yum install xxx 指定下载安装xxx软件
```



