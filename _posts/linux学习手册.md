# linux学习手册

## 一、网卡配置

```
ifconfig
systemctl restart  network   重启网卡（service network restart）
网卡配置文件路径：/etc/sysconfig/network-scripts/网卡地址文件
DNS配置文件路径：/etc/resolv.conf
网卡回环地址路径：/etcsysconfig/network-scripts/ifcfg-lo
设置主机和IP绑定信息：/etc/hosts
设置主机名：/etc/hostname
```

### 1.临时修改网卡IP地址

```
ifconfig 网卡名称 IP地址    ------此功能重启系统或重启网卡失效
```



### 2.添加多个临时IP地址

```
ifconfig 网卡名称:0 IP地址 
ifconfig 网卡名称:1 IP地址 

```

### 3.删除临时IP地址

```
ipconfig 网卡名称:0 del IP地址
```



### 4.NetworkManager

NetworkManager是管理和监控网络设置的守护进程

```
ststemctl status NetworkManager   查看NetworkManager启动状态
```



### 5.永久修改网卡地址：systemctl restart  network   重启网卡

#### 方法1：使用nmtui文本框方式修改IP（网络管理器，界面化配置）

```
nmtui
```



#### 方法2：修改网卡配置文件修改IP

```
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```



## 二、防火墙调试

### 1.关闭防火墙并设置开机不启动

```
systemctl status firewalld.service   查看防火墙当前状态
systemctl stop firewalld.service    关闭防火墙
systemctl start firewalld.service   启动防火墙
systemctl restart firewalld.service    重启防火墙
systemctl disable firewalld.service   关闭防火墙开机自启
systemctl enable firewalld.service   开启防火墙开机启动
```



### 2.临时关闭和永久关闭SeLinux

```
getenforce   #查看selinux启动状态
setenforce 0 #临时关闭selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config    #永久关闭selinux，重启执行
```



## 三、光盘开机自动挂载

vi /etc/fstab

```
/dev/cdrom             /mnt           iso9660  defaults   0   0
```

ls /mnt   查看是否挂载成功

## 四、配置本地yum源

yum 的一切配置信息都存储在一个yum.repos.d文件夹下的配置文件中，通常位于  /etc/yum.repos.d/   ，需将repo文件重命名.bak

vim Centos7.repo

```
[Centos7]
name = Centos-server
baseurl = file:///mnt
enabled = 1
gpgcheck = 0
```

**参数说明：**

[Centos7]     →  yum的IP，必须唯一

name = Centos-server     →描述信息

baseurl = file:///mnt    →  /mnt表示光盘挂载点，file后面为///

enabled = 1      →启用

gpgcheck = 0     →是否认证   0为取消认证

**清空并生成缓存列表：**

```
yum clean all    清空yum缓存
yum list  生成缓存列表
验证为：yum install -y httpd
```



## 五、Linux基本命令操作

### 5.1 Linux终端介绍

通过tty可查看当前终端信息

不同终端之间的通讯： echo hello > /dev/pts/1    方式二：  wall “内容”

### 5.2 认识shell

用户输入命令提交至shell，内部命令直接调用或在系统中查找该命令调用内核中执行（type区分内部命令与外部命令 例如：type 命令）

shell提示符  # 与$的区别：#为root用户   $为普通用户   

```
su 用户名   #切换用户
```

查看所有的shell类型   cat /etc/shells

### 5.3 基本命令

命令   【选项】  【参数】

#### 基础命令ls

```
作用：查看当前目录下有哪些文件（list）
语法：ls 目录/文件，如果什么也不加，那么查看的是当前目录下的内容
常用选项：
第一个字母为文件类型：d：目录文件   l：链接文件   b：块设备文件   c:字符设备文件   p:管道文件    -：普通文件
ls    查看当前目录下内容
ls -l  列出文件的详细信息，如创建者，创建时间，文件的读写权限等  可简写为ll
ls -a 列出目录下所有文件，包括以.结尾的隐藏文件  ..文件为父目录隐藏
ls -d  查看当前目录，不查看内容
ls -lS  从大到小排序
ll /目录   列举指定目录下内容
```

#### 基础命令alias作用：别名

#### 基础命令cd

```
作用：切换目录
语法：cd 目录/目录/目录
单cd直接返回家目录
cd .. 表示返回上级目录，也就是父目录
cd -  表示切换至切换前的目录
```

#### 基础命令history

```
作用：查看执行历史
快速查找方法1： ctrl+r //搜索历史命令关键字
快速查找方法2： !168   搜索第N条命令
快速查找方法3： !搜索历史命令关键字
```

#### linux下快捷键：

```
ctrl+c  停止当前操作
ctrl+d  退出，等价于exit
ctrl+l   清屏  相当于clear
ctrl+a  调到命令行开头
ctrl+e  调到命令行的末尾
ctrl+r   搜索历史命令
TAB     补全命令  连按两次列举命令
```

## 六、修改系统时间

```
hwclock   查看bios时间
date   查看系统时间
date --help   查看date帮助
date 常用参数：
-s  "字符串"    #把时间设置为字符串所描述的时间
%F 完整日期格式，等价于%Y-%m-%d
%m  分钟
%Y   年份
%y   年份最后两位
%d   天
%M   月份
%H   小时
```

## 七、测试命令使用时间

```
time 作用：一般用来测量一个命令的运行时间
使用方法：time后面直接加上命令和参数即可
参数：real-总时间    user-用户层时间  sys-内核状态使用时间
```

## 八、Help帮助命令

### 1.man 

```
man 命令
查看命令支持 上下翻页，退出q
```

### 2.-h  --help

## 九、开关机命令及7个启动级别

### 1.关机命令之shutdown

```
作用：关机、重启、定时关机
语法：shutdown 【选项】
参数：-r 重启计算机   -h关机   -h 【时间】  定时关机（-r【时间】定时重启）
shutdown -c   取消定时关机任务
```

### 2.系统启动级别

```
init 0-6
0系统停机模式，系统默认运行级别不能设置为0，否则不能正常启动，机器关的
1单用户模式。root权限，用于系统维护，禁止远程登陆
2多用户模式，没有nfs和网络支持
3完整的多用户模式，有nfs和网络支持，登陆后默认进入命令行模式
4系统未使用，保留不用，特殊情况下使用
5图形化模式，登陆后进入gui模式
6重启模式，默认运行模式不能设置为6  否则不能正常启动，运行init 6  机器会重启
设置默认启动等级：
systemctl set-default multi-user.target （设置为3级别，其他级别需百度）
查看当前默认启动级别： runlevel  或者systemctl get-default 
```

### 3.设置服务器来电后自动 开机

bios中restore on ac power loss  修改为power on

### 4.设置服务器定时开机

power management setup 电源管理设置

wake up event setup 选择resume rtc alarm 修改为enable

### 5.设置服务器自动关机



## 十、Linux系统目录

### 1.目录介绍

```
tree查看linux目录结构（默认没有安装，yum -y install tree）
/ 处于linux系统树形结构的最顶端，它是linux文件系统的入口，所有的目录，文件，设备都在
/bin 常用的二进制命令目录，比如 ls cp  mkdir等
/boot 存放的系统启动相关的文件，例如 kernel.grub引导装在程序
/dev 设备文件目录比如声卡，磁盘等硬件设备
/etc 常用系统及二进制安装包配置文件和服务器启动命令目录
/home 普通用户的家目录存放目录
/lib 库文件存放目录，函数库目录
/mnt 一般用来挂载存储设备的挂载目录，比如cdrom U盘等
/opt 可选择，有些软件包会安装在这里
/proc 系统运行时，进程信息及内核信息（CPU,硬盘分区，内存等）
/sys 系统目录，存放硬件信息的配置文件
/run 运行目录，存放的是系统运行时的数据 比如进程的PID
/srv 服务目录，存放的是我们本地服务的相关文件
/sbin 涉及系统管理的命令，超级权限用户root权限
/tmp 该目录存放临时文件，运行程序时产生的临时文件
/var 系统及软件运行时产生的日志信息
/usr 存放应用程序和文件 
/usr/bin 普通用户使用的应用程序 
/usr/sbin 管理员使用的应用程序 
/usr/lib 库文件32位
/usr/lib64 库文件Glibc
usr目录下 /lib 系统基本的动态链接共享库
```

### 2.绝对路径和相对路径

```
绝对路径：从根/下开始
相对路径：相对于当前路径，以.或者..开始
```

### 3.文件的管理

#### 3.1创建文件或文件夹

```
touch 用来创建空文件，如果文件已存在，会修改文件修改时间touch -d 【时间】
mdkir 创建目录   mkdir -p /A/B/C/D 创建多层级目录
```

#### 3.2删除文件和目录

```
rm 
选项：
-f 强制删除，没有提示 
-r 删除目录
rm -rf 使用前务必自行确认文件下信息
```

#### 3.3复制文件

```
cp 源文件/目录 目的文件/目录
-R/r 递归处理，将目录下所有文件与子目录一起处理
```

#### 3.4移动文件

```
mv 
mv a.txt /dir/b.txt   移动支持改名操作
```

#### 3.5查看文件

```
cat 【文件名】  查看文件内容，一次显示整个文件的内容
more 【文件名】  以行数显示，回车刷新一行  空格刷新一屏   q退出
less 【文件名】  支持前后翻页 UP DOWN按键操作   q退出
head 【文件名】  显示文件开头内容   -n显示文件开头几行，默认10行
tail 【文件名】   显示文件末尾内容    -n显示文件末尾几行，默认10行  -f动态显示数据（tailf）
```

#### 3.6 Xfs文件系统 的备份与恢复 

```
XFS提供xfsdump和xfsrestore工具协助备份xfs文件系统中的数据（centos7下）
0代表：完全备份
1-9代表：增量备份
```

```
例：

1.新加磁盘格式化 
ls /dev/sd*  （默认sdb为新增盘） 
fdisk /dev/sdb   p查看 n新建  w保存
2.创建挂载点  
mkfs.xfs /dev/sdb1    
mkdir /sdb1新建文件夹  
mount /dev/sdb1/挂载  
3.备份（-L备份标头  -M媒体标头）
xfsdump -f  备份存放位置 要备份路径  xfsdump -f /opt/dump_sdb1 /sdb1 -L dump_sdb1 -M sdb1
4.测试恢复，恢复备份（先删除内容后恢复）




```

