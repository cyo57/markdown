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

改变的群组名称必须要在/etc/group文件内存在

`chgrp -R` 递回，即连同目录下的所有文件

- `chown` - 改变文件拥有者

`chown binbin file/dir` 将文件所属者修改为binbin

chown也可以使用`chown user.group file` 将会同时修改所属群组

或者使用（推荐）`chown user:group file` 同时修改群组

`-R` 递回，即连同目录下的所有文件

- `chmod` - 改变权限

`chmod a+x file/dir` 所有人可执行

`chmod a-rw file/dir` 所有人不可读写

`-R` 递回，即连同目录下的所有文件

权限对**目录**的重要性

**对目录只有`r`权限，只能读取目录树，无法读取文件。进入目录需要`x`权限**

例题：假设有个帐号名称为cyo57，他的主文件夹在/home/cyo57/，cyo57对此目录具有`rwx`的权限

```bash
-rwx------ 1 root  root  4365 Sep 19 23:20  the_root.data
```

对此文件来说是cyo57 是 others 的身份，无法rwx。但是此文件在他的主文件夹下，他对此目录具有rwx的权限。因此他可以删除这个文件，但无法打开查看。

假设 cyo57 对某目录拥有 r--，但对某目录下的文件拥有rwx

```bash
drw-r--r--.  2 root  root    32 11月 16 21:30 rootdir
```

```bash
-rwxrwxrwx. 1 root root 12 11月 16 21:29 1.txt
-rwxrwxrwx. 1 root root  9 11月 16 21:30 2.txt
```

尽管cyo57拥有对 `1.txt` 和 `2.txt` 的 `rwx` 权限，但用户缺乏对 `rootdir` 目录的执行权限，**不允许**用户进入该目录，也就无法读取、写入、执行其中的文件了

#### 数字类型改变文件权限

Linux文件的基本权限只有九个，分别是owner/group/others的read/write/execute权限，缩写为-rwxrwxrwx三个一组。我们也可以使用数字来代表权限

> r:4, w:2, x:1

如果同时拥有`rwx`权限则是7，（因为是二进制的`111`）
如果某文件的权限为`777`则等同于`-rwxrwxrwx`

#### 符号类型改变文件权限

```bash
| chmod | u g o a | +（加入） -（除去） =（设置） | r w x | 文件或目录 |
```

user, group, others, all

例如设置一个文件权限为`-rwxr-xr-x`
`chmod u=rwx,go=rx file`即可，但需要注意u,go是**连在一起**的，**没有空格**

例如移除全部人的可执行权限：`chmod a-x file`

#### 权限的意义

**权限对文件的重要性**

- r : 读取内容，例如文本

- w : 允许编辑、新增或修改（**不含删除**）

- x : 可被系统执行

**权限对目录的重要性**

- r : 读取目录清单（不含文件内容）

- w : 这个权限对目录来说很多
  
  - 创建文件与目录
  
  - 删除文件与目录（与目录下**文件本身**的权限无关）
  
  - 将已存在的文件目录更名
  
  - 移动文件和目录

- x : 表示能否进入该目录，也就是`cd`到该目录（影响读取目录下的文件内容）

#### 文件种类

- 正规文件
  
  - 纯文本文件 (ASCII)
  
  - 二进制文件 (binary)
    可执行文件
  
  - 数据格式文件 (data)
    某些特定格式的文件，例如登录的数据记录在 `/var/log/wtmp` 文件内，用`last`可读，但`cat`会乱码

- 目录 (directory)
  例如`drwxrwxrwx`

- 链接文件 (link)
  例如`lrwxrwxrwx`

- 设备与设备文件 (device)
  
  - 区块设备文件 (block)
    硬盘和软盘，例如`/dev/sda`

- 字符设备文件 (character)
  串行端口 (COM接口) 的周边设备，例如键盘鼠标，不能截断输出

- 数据接口文件 (sockets)

- 数据输送档 (FIFO, pipe)
  第一个属性为`p`，主要目的是解决多个程序同时存取一个文件造成的错误，全称 first-in-first-out

**Linux的文件拓展名**

Linux的文件能不能执行，与后缀是无关的。只和文件属性有关。

当然我们仍然借拓展名了解文件内容，通常还是会用拓展名表示该文件是什么种种类。常用的拓展名：

- .sh
  脚本和批处理

- Z, tar, tar.gz, zup, tgz
  经过打包的压缩文件，多用于gunzip, tar等等

- .hmtl, .php

文件名只是让你了解该文件可能的用途而已， 真正的执行与否仍然需要权限的规范！例如常见的/bin/ls这个指令，如果这个文件的权限被修改成无法执行时， 那么ls就不能运行了

Linux下传统Ext2/Ext3/Ext4和xfs，文件名长度限制为255Bytes，也就是255个ASCII字符或128汉字

### 目录配置

目前常用Filesystem Hierarchy Standard （FHS）标准

|                        | 可分享的（shareable）     | 不可分享的（unshareable） |
| ---------------------- | ------------------- | ------------------ |
| 不变的（static）            | /usr （软件放置处）        | /etc （配置文件）        |
| /opt （第三方协力软件）         | /boot （开机与核心档）      |                    |
| 可变动的（variable）         | /var/mail （使用者邮件信箱） | /var/run （程序相关）    |
| /var/spool/news （新闻群组） | /var/lock （程序相关）    |                    |

- / （root, 根目录）：与开机系统有关；
- /usr （unix software resource）：与软件安装/执行有关；
- /var （variable）：与系统运行过程有关。

| 目录                  | 应放置文件内容                                                                                                    |
| ------------------- | ---------------------------------------------------------------------------------------------------------- |
| 第一部份：FHS 要求必须要存在的目录 |                                                                                                            |
| /bin                | 可执行文件的目录                                                                                                   |
| /boot               | Linux核心文件以及开机菜单与开机所需配置文件等等。                                                                                |
| /dev                | 任何设备与周边设备都是以文件的型态存在于这个目录当中的。存取目录下面的某个文件，就等于存取某个设备                                                          |
| /etc                | 系统主要的配置文件，例如人员的帐号密码档只有root有权力修改                                                                            |
| /lib                | 开机时会用到的函数库， 以及在/bin或/sbin下面的指令会调用的函数库<br/>必须要存在：/lib/modules/：放置可抽换式的核心相关模块（驱动程序）                          |
| /media              | 可移除的设备，包括软盘光盘DVD等等                                                                                         |
| /mnt                | 暂时挂载某些额外设备，建议放置到这个目录                                                                                       |
| /opt                | 第三方协力软件                                                                                                    |
| /run                | 系统开机后所产生的各项信息                                                                                              |
| /sbin               | 包括开机、修复、还原系统所需要的指令，某些服务器软件程序，一般则放置到/usr/sbin/当中。本机自行安装的软件所产生的系统可执行文件（system binary）放置到/usr/local/sbin/当中了。 |
| /srv                | “service”的缩写，是一些网络服务启动之后，这些服务所需要取用的数据目录                                                                    |
| /tmp                | 暂时放置文件的地方。 任何人都能够存取                                                                                        |
| /usr                | 第二层 FHS 设置，后续介绍                                                                                            |
| /var                | 第二层 FHS 设置，主要为放置变动性的数据，后续介绍                                                                                |
| 第二部份：FHS 建议可以存在的目录  |                                                                                                            |
| /home               | 系统默认的使用者主文件<br/>`~`代表目前这个使用者的主文件夹                                                                          |
| /lib<qual>          | 与 /lib 不同的格式的二进制函数库，例如 64 位的 /lib64 函数库等                                                                   |
| /root               | root的主文件夹                                                                                                  |

- /usr 的意义 (Unix Software Resource)

| 目录                 | 应放置文件内容                                                                                                            |
| ------------------ | ------------------------------------------------------------------------------------------------------------------ |
| FHS 要求存在           |                                                                                                                    |
| /usr/bin/          | 一般用户能够使用的指令                                                                                                        |
| /usr/lib/          | 与 /lib 功能相同， /lib 就是链接到此目录中                                                                                        |
| /usr/local/        | 自行安装的软件                                                                                                            |
| /usr/sbin/         | 目前 /sbin 就是链接到此目录中的                                                                                                |
| /usr/share/        | 主要放置只读架构的数据文件<br/>常见的还有这些次目录：/usr/share/man：线上说明文档 <br>/usr/share/doc：软件杂项的文件说明<br/>/usr/share/zoneinfo：与时区有关的时区文件 |
| 第二部份：FHS 建议可以存在的目录 |                                                                                                                    |
| /usr/games/        | 与游戏比较相关的数据放置处                                                                                                      |
| /usr/include/      | c/c++等语言的（header）与（include）                                                                                        |
| /usr/libexec/      | 不被一般使用者惯用的可执行文件或脚本                                                                                                 |
| /usr/lib<qual>/    | 目前 /lib<qual> 就是链接到此目录中                                                                                            |
| /usr/src/          | 一般源代码，src有source的意思。核心源代码则建议放置到/usr/src/linux/                                                                     |

- var 的意义

系统运作后才会渐渐占用硬盘，`/var` 主要针对常态性变动的文件，包括 cache, log file以及某些软件运行所产生的lock file, run file。常见的次目录

| 目录          | 应放置文件内容                                                     |
| ----------- | ----------------------------------------------------------- |
| FHS 要求必须存在  |                                                             |
| /var/cache/ | 应用程序本身运行过程中产生的一些暂存盘                                         |
| /var/lib/   | 程序执行的过程中使用到的数据文件放置，有各自的目录。 举例，MySQL的数据库放置到/var/lib/mysql/   |
| /var/lock/  | 目前此目录也已经挪到 /run/lock 中                                      |
| /var/log/   | 登录文件放置！比较重要的文件如/var/log/messages, /var/log/wtmp（记录登陆者的信息）等。 |
| /var/mail/  | 也被放置到/var/spool/mail/目录中！ 通常这两个目录是互为链接文件啦！                  |
| /var/run/   | PID放置在这个目录下，这个目录链接到 /run 去了！                                |
| /var/spool/ |                                                             |

![](../assets/2023-11-17-09-51-41-centos7_0210filepermission_4.jpg)

### 回顾

- 文件和目录的`rwx` 权限 和 群组
  `chown`, `chgrp`, `chmod`
  `chmod` 的符号法与数字法
  开放浏览目录至少需要`rx`权限，如果目录没有`x`权限，目录下的文件无法读取

- `drwxrwxrwx` 第一位字母的含义

- `.file` 作为隐藏文件

- FHS所定义的三层主目录为：/, /var, /usr三层

## 路径与目录

### 目录和路径

#### 绝对路径与相对路径

写程序 （shell scripts） 来管理系统的条件下，务必使用绝对路径的写法。这个写法绝对不会有问题

#### 目录相关操作

- . 代表此目录

- .. 代表上一个目录

- \- 代表前一个工作目录

- ~ 代表目前用户的主目录

- ~account 代表account用户的主目录

#### $PATH 变量

运行`echo $PATH`查看哪些目录被定义，`$`后接的是变量，`$PATH` 和 `${PATH}`是完全相同的。并且每个目录是有“顺序”之分的

运行目录下的二进制必须 `./file` ，直接`file` 是不可行的

添加环境变量目录很简单

```bash
PATH="${PATH}:/dirname"
```

### 文件与目录管理

#### ls

`ls -l` 分别显示 权限、链接次数(i-node)、所属用户、所属组、长度（目录下的文件数）文件修改日期、

`ls` 的用法还有很多，包括查阅文件所在 i-node 号码的 `ls -i` 选项，以及用来进行文件排序的 `-S` 选项，还有用来查阅不同时间的动作的 `--time=atime` 等

#### cp, rm, mv

- cp

> -a ：相当于 -dr --preserve=all 的意思，至于 dr 请参考下列说明；（常用）
> -d ：若来源文件为链接文件的属性（link file），则复制链接文件属性而非文件本身；
> -p  ：连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性（备份常用）；
> -r  ：递回持续复制，用于目录的复制行为；（常用）
> -l  ：进行硬式链接（hard link）的链接文件创建，而非复制文件本身；
> -s  ：复制成为符号链接文件 （symbolic link），亦即“捷径”文件；(`ls -l` 会显示 `->`)R

如果来源文件有两个以上，则最后一个目的文件一定要是“目录”才行！
对于**不同身份**来说，需要特别注意的特殊权限文件，必须要加上 -a 或者是 -p 等等可以完整复制文件权限的选项

- rm

> -f : 即force，忽略警告
> -i : 互动
> -r : 递回，删除目录

- mv

> -f : force
> -i : 询问是否覆盖
> -u : 覆盖文件时，若source比较新才会覆盖

#### 获取路径的文件名和目录名

- `basename /dir/dir/file`

- `dirname /dir/dir`

### 文件内容查阅

#### 检视

- cat 由第一行开始显示文件内容
- tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
- nl 显示的时候，顺道输出行号！
- more 一页一页的显示文件内容

>  more最有用的功能是按下 `/`进行向下搜索，重复搜索就直接按下`n`

- less 与 more 类似，但是比 more 更好的是，他可以往前翻页！

> `/`向下搜索，`?`向上搜索

- head 只看头几行
- tail 只看尾巴几行

> 可以`tail -f file`侦测这个文件，新加入的数据都会显示到屏幕上

- od 以二进制的方式读取文件内容！

例题：显示11-20行的内容
`head -n 20 /etc/man_db.conf | tail -n 10`
管线的作用就是通过 `|` 让后续命令继续使用前者输出的信息

例题：显示11-20行的内容并带行号
`head -n 20 /etc/man_db.conf | tail -n 10`

#### 修改文件时间

- **modification time （mtime）**：
  内容变更时更新。不包含文件属性或权限
  `ls -l`

- **status time （ctime）**：
  状态改变时更新，例如权限和属性
  `ls -l --time=ctime`

- **access time （atime）**：
  读取时间
  `ls -l --time=atime`

#### 默认权限与隐藏权限

除了`rwx` 基本权限外，在Linux传统的`Ext`文件系统下可以用`chattr`进行设置，以`lsattr`查看，其中最重要的属性就是**不可修改**，即使是文件拥有者也不可修改

#### umask 文件默认权限

umask 就是指定目前使用者在创建文件或目录时候的**权限默认值**

- 查看umask

`umask`以数字类型表示，`umask -S` 以符号类型表示。其中`umask`的第一位数就是特殊权限

    [root@localhost ~]# umask
    0022
    [root@localhost ~]# umask -S
    u=rwx,g=rx,o=rx

umask的分数指的是“**默认值**需要**减掉**的权限”。文件默认是`666`，目录默认是`777`，如果文件减去`022(u, g-w, o-w)`也就是`user`没有任何影响，`group`和`others`的权限减去2

- 对文件：`-rw-rw-rw-` -> `-rw-r--r--`

- 对目录：`drwxrwxrwx` -> `drwxr-xr-x`

假如我们要修改`umask`值，直接运行`umask 002`即可，注意运算时是减去
