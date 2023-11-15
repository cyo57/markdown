# Linux-Basic

## 安装Linux distribution

KVM

### CentOS 7

- 如果磁盘容量小于2TB的话，系统会默认使用MBR模式安装，如果需要GPT的话：
  
  1. 安装前按Tab加入额外的参数 `inst.gpt`

#### LVM机制与标准分区

- 标准分区区

- LVM

- LVM进阶

#### 回顾

- 建议安装时至少有 `/`,`/boot`, `/home`, `swap` 四个分区

- CentOS 7安装模式至少有两种，分别是图形与命令行

- CentOS 7若小于2T硬盘，默认使用MBR模式，当然也可以强迫使用GPT

- 自行规划分区

- 一般swap大小为1.5~2倍的物理内存

- CentOS 7默认使用 xfs 文件系统

- 不启用kdump，因为是核心开发者查阅死机数据

- 尽量使用一般用户，有必要再转身份成为root

- root 密码不要随便设置！即使是联系机

习题

- Linux的目录配置以树状目录配置，在默认情况下，安装系统时要求一定要分区的Partition是？

> / 和 swap

- 如果磁盘使用 MBR 方式分区，且设置了四个 Primary 分区，且磁盘还有空间，是否还能使用这些空间

> 不行，因为MBR最多只有四个 Primary 分区，而且没有 Extended 自然不能再使用 Logical 分区

## 首次登录

CentOS默认进入tty1，不论是用GUI还是命令行

#### 语系支持

有时候输入指令后提示乱码，但实际上Linux支持多国语言。

默认情况下 terminal 不支持中文编码输出，我们可以查看目前支持的语系

```bash
$ locale
```

修改语系只需要

```bash
$ LANG=en_US.utf8
$ export LC_ALL=en_US.utf8
```

将会在本次登陆中生效，如果登出后是会还原回系统默认的

### 基础命令

- data - 查看时间

格式化输出

```bash
[root@localhost ~]$ date +%Y/%m/%d
2023/11/15
[root@localhost ~]$ date +%H:%M
19:30
```

- cal - 查看日历

查看2015年10月的日历 `cal 10 2015`

- bc - 计算器

默认是整数模式，如果需要显示小数，则需要执行 `scale=number` , number就是小数点位数

- 终端快捷键

ctrl - c

ctrl - d 等同于exit

tab

shift + pgup/dn 翻页

### man page与info page

#### --help

大多数程序就将此命令写入程序中了

#### man page

如果 --help 没有解决，那就man 他

例如 `man date`, 按 q 离开man的环境

输入`/word` 搜寻关键字，例如`/date`。输入`?word`以倒序搜寻

**区段**

区段1：用户指令
区段2：系统调用
区段3：程序库调用
区段4：设备
区段5：文件格式
区段6：游戏
区段7：杂项
区段8：系统指令
区段9：内核内部指令
区段n：Tcl或Tk指令

#### info

以段落格式的说明，例如`info info`。

可以通过 `tab` 键在各个超链接间移动

#### doc

/usr/share/doc

### nano

### 电源管理

#### 关机

- `sync` - 数据写入磁盘

- `shutdown -h +{sec}`
  `shutdown -h {hh:mm}`
  `shutdown -r now '我要关机了'`
  `shutdown -k now '不关机但是给你发提醒'`

#### 重启

- `sync`

- `reboot`

## Linux的文件权限与目录配置

Linux一般将文件可存取身份分为三类，分别是

- owner/group/others

三种身份分别对应为

- read/write/execute

### 使用者与群组

1. 文件拥有者

2. 群组

3. 其他人

**root用户啥都能管**

- /etc/passwd
  所有账号的密码

- /etc/shadow
  个人密码

- /etc/group
  群组名称记录

### 文件属性

```bash
[cyo57@localhost ~]$ sudo ls -hl
总用量 0
drwxr-xr-x. 2 cyo57 cyo57  6 11月 15 16:01 公共
drwxr-xr-x. 2 cyo57 cyo57  6 11月 15 16:01 模板
```

其中drwxr-xr-x

1. 权限
   
   - d 代表目录
   
   - \- 代表文件
   
   - l 代表链接文件
   
   - b 代表可随机存取设备
   
   - c 代表一次性读取设备
   
   - rwx三位一组，分别代表owner、group、other的r/w/x权限

2. 表示有多少文件名连接到此

3. 表示拥有者账号

4. 表示群组

5. 容量大小 (Bytes)

6. 最近修改日期

7. 文件名

**改变文件属性和权限**

- `chgrp` - 改变群组

- `chown` - 改变文件拥有者
  
  - chown也可以使用`chown user.group file` 同时修改群组

- `chmod` - 改变权限

`+r` `-x` 加减

`chmod a+w` #所有人都可写



**权限对目录的重要性**

**只有`r`权限，只能读取目录树，无法读取文件。进入目录需要`x`权限**



例题：假设有个帐号名称为dmtsai，他的主文件夹在/home/dmtsai/，dmtsai对此目录具有`rwx`的权限

```bash
-rwx------ 1 root  root  4365 Sep 19 23:20  the_root.data
```

对此文件来说是dmtsai 是 “others”的身份，无法rwx。但是此文件在他的主文件夹下，他对此目录下具有rwx的权限。因此他可以删除这个文件，但无法打开查看。
