# Linux系统

## 文件路径特点

![](assets/Linux1.png)

## 远程连接操作

通过远程连接工具连接到服务器的操作即为远程连接

条件：

1.服务器的IP地址

2.服务器的账号和密码

注意：必须要确保有网络连接条件

Xshell（个人免费/商用收费）、FinalShell

所有用于远程连接的ip地址的最后一位均不能为1

默认情况下的centos7不支持ifconfig，需要按照运行环境才可，

默认为ip addr

在一个网络范围内，一般由1~255个ip地址，其中1和255会被入网和出网设备占用

Linux发行：在原版Linux系统的基础上，额外增加一些常用软件的操作系统

Redhat中的为centos    Debian中的为Ubuntu

## Linux命令

![](assets/Linux2.png)

**命令格式：**

![](assets/Linux3.png)

查阅命令的帮助信息

command --help

查阅使用手册

man command

![](assets/Linux4.png)

![](assets/Linux6.png)

pwd：查看当前路径

ls：查看当代目录下的哪些文件和文件夹

![](assets/1765460911023.png)

mkdir：创建文件夹，后面可接一个或多个参数

cd：切换目录

![](assets/Linux9.png)

![](assets/Linux7.png)

touch：创建文件

mv：创建或移动文件

![](assets/Linux8.png)

![](assets/Linux10.png)

**相对路径和绝对路径：**

相对路径：路径信息以'.'或'..'开头的均为相对路径

绝对路径：路径信息以'/'或'~'开头的均为绝对路径

cp：复制文件

![](assets/Linux11.png)

rm：删除文件或目录

![](assets/Linux12.png)

重定向

![](assets/1765461115683.png)

![](assets/1765461124897.png)

cat查看内容

![](assets/1765461169169.png)

![](assets/1765461178122.png)

分屏显示内容less

![](assets/1765461405583.png)

![](assets/1765461415396.png)

![](assets/1765461477288.png)

grep查找文件信息

![](assets/1765461706951.png)

管道|

![](assets/1765461737375.png)

head从前查找内容

![](assets/1765462055342.png)

tail从后查找命令

![](assets/1765462090575.png)

**多用于日志文件查询**

动态查询日志文件 tail -f

![](assets/1765634266912.png)

**查看进程命令：**

![](assets/1765634692958.png)

![](assets/1765634718982.png)

运行程序:  ./程序名

![](assets/1765635219308.png)

nmon_x86_64_centos7

查找运行的程序的进程id

![](assets/1765635383988.png)

![](assets/1765635441492.png)

**动态检测任务管理器：**

top

![](assets/1765635499881.png)

![](assets/1765635760734.png)

常见端口号：

http：80

https：443

ssh：22

mysql：3306

**端口查询netstat**

![](assets/1765635840949.png)

![](assets/1765635879052.png)

![](assets/1765635889499.png)

**lsof命令：**

![](assets/1765636105051.png)

which命令寻找程序所在目录

![](assets/1765636328996.png)

切换用户su

![](assets/1765636359966.png)

whoami当前用户是谁

![](assets/1765636476125.png)



