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



## 磁盘与文件系统管理

#### Linux 的 EXT2 文件系统 (inode)

文件系统一开始就将 inode 和 block 规划好了，除非重新格式化，否则 inode 和 block 就不再变动。
Ext2 中假设某个文件的属性与权限在 inode4 ，而这个 inode 记录了文件数据的实际放置点

![inode/block 数据存取示意图](filesystem-1-1700483198515-6.jpg)

inode 记录的文件数据至少有：

> r, w, x
> group
> 容量
> ctime, atime, mtime
> flag (例如Set UID)
> 指向 (pointer)

每个文件都会占用一个 inode, 包括新的 ACL 和 SELinux

#### Linux 文件系统的运行

Linux 中采用的方式是通过非同步处理 (asynchronously)：

> 当系统载入一个文件到内存后，如果该文件没有被更动过，则在内存区段的文件数据会被设置为干净（clean）的。  但如果内存中的文件数据被更改过了（例如你用 nano 去编辑过这个文件），此时该内存中的数据会被设置为脏的  （Dirty）。此时所有的动作都还在内存中执行，并没有写入到磁盘中！  系统会不定时的将内存中设置为“Dirty”的数据写回磁盘，以保持磁盘与内存数据的一致性。 你也可以利用[第四章谈到的 sync](https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/Text/index.html#sync)指令来手动强迫写入磁盘。

- 系统会将常用的文件数据放置到内存的缓冲区
- Linux 的实体内存最后都会被用光，加速系统性能
- 可以手动 `sync` 强迫内存中 Dirty 的文件回写到磁盘
- 正常关机时会主动调用 `sync` 将内存的数据回写入磁盘
- 不正常关机（如跳电、死机或其他不明原因），由于数据尚未回写到磁盘内， 因此重新开机后可能会花很多时间在进行磁盘检验，甚至可能导致文件系统的损毁（非磁盘损毁）。

#### 挂载点的意义 (mount point)

挂载点一定是**目录**。该目录为该文件系统的入口。因此并不是任何文件系统都能使用，必须要挂载到目录树的某个目录，才能够使用该文件系统。
举例来说，查看/, /boot, /home

```bash
[root@localhost ~]# ls -lid / /boot /home
      64 dr-xr-xr-x. 18 root root  238 11月 19 21:29 /
      64 dr-xr-xr-x.  5 root root 4096 11月 19 20:19 /boot
50331763 drwxr-xr-x.  6 root root   58 11月 19 20:33 /home
```

最前面的数字就是 inode，因此可以发现/boot 和/home 是不同的文件系统。同一个filesystem的某个 inode 只会对应到一个文件内容。可以通过 inode 号码来确认是否为相同文件：

```bash
[root@localhost ~]# ls -lid / /. /..
64 dr-xr-xr-x. 18 root root 238 11月 19 21:29 /
64 dr-xr-xr-x. 18 root root 238 11月 19 21:29 /.
64 dr-xr-xr-x. 18 root root 238 11月 19 21:29 /..
```

可以看到三个文件均在同一个 filesystem 内，inode 均为64，也就是在根目录下，三个路径是一模一样的

查看本机 Linux 支持的文件系统和已载入到内存中支持的：

```bash
ls -l /lib/modules/$（uname -r）/kernel/fs
cat /proc/filesystems
```

#### Linux VFS

![VFS 文件系统的示意图](centos7_vfs-1700483125456-3-1700483222300-9.gif)

#### XFS 文件系统he

CentOS 7开始，默认文件系统由 EXT4 转换为 XFS。

heExt 文件系统采用的是预先规划出所有的 inodhee/block/meta data等数据，未来直接取用不再动态配置。而 XFS 文件系统是动态产生，所以格式化超级快。

另外 XFS 的 block 和 inode 由多种不同的容量可以设置

### 文件系统的简单操作

#### 磁盘与目录的容量

磁盘整体数据在 superblock 区块中，但每个个别文件的容量在 inode 中。

- `df` 列出文件系统的整体磁盘使用量
- `du` 评估文件系统的磁盘使用量

> -a : 列出所有的文件与目录容量，因为默认仅统计目录下面的文件量
> -s  ：列出总量，不列出每个目录占用
> -S  ：不包括子目录下的总计，与 -s 有点差别。



#### `ln` 硬链接与符号链接

Linux 下的链接文件有两种，一种是类似于 Windows 快捷方式的文件，另一种是通过文件系统 inode 链接来产生新文件名，而不是新文件。

![实体链接的文件读取示意图](hard_link1-1700487068474-13.gif)

- Hard Link

多个文件名对应到同一个 inode 号码，只是在某个目录下新增一笔文件名链接到某 inode 号码的关连记录而已。除了文件名所有相关信息一模一样。
由图可知， hardlink 应该仅能在单一文件系统中进行，不能跨文件系统，也不能link目录

- Symbolic Link

和 Windows 快捷方式类似

![symbolic_link1](symbolic_link1-1700487223070-18-1700487225105-20-1700487226461-22-1700487227824-24.gif)

> [root@study ~]# ln [-sf] 来源文件 目标文件
> 选项与参数：
> -s  ：如果不加任何参数就进行链接，那就是hard link，至于 -s 就是symbolic link
> -f  ：如果 目标文件 存在时，就主动的将目标文件直接移除后再创建！

把 /etc/passwd 复制到 /tmp 观察inode 和 block
创建硬链接到 passwd-hl 和 软链接到 passwd-sl

```bash
[root@localhost tmp]# ll -i | grep passwd
17195826 -rw-r--r--. 2 root  root    2385 11月 20 20:28 passwd
17195826 -rw-r--r--. 2 root  root    2385 11月 20 20:28 passwd-hl
17197625 lrwxrwxrwx. 1 root  root       6 11月 20 20:29 passwd-sl -> passwd
```

用 `ls -i` 查看inode 可知为同一个文件

**关于目录的 link 数量**

创建一个新目录名为 /tmp/test 时，目录会有三个东西

```bash
/tmp/test
/tmp/test/.
/tmp/test/..
```

其中 `/.` 和 `/tmp/test/` 是一样的，`/tmp/test/..` 代表 `/tmp` ，所以新目录 link 数为 2，上层目录 link 会+1

### 分区、格式化、检验、挂载

Linux 中，磁盘名通常为 /dev/sd[a-p]

#### 观察磁盘分区状态

- 列出系统上所有磁盘列表

`lsblk` (list block device)
`blkid` 列出设备UUID (或 `lsblk -f`)
`parted /dev/sda print` 列出磁盘的分区表类型和分区信息



#### gdisk/fdisk 分区

> MBR 使用 fdisk 分区，GPT 使用 gdisk 分区

- 用 `gdisk` 新增分区
  1GB 的 xfs 文件系统
  1GB 的 vfat 文件系统
  0.5GB 的 swap （等下会删除）

```bash
[root@localhost ~]# gdisk /dev/sdb 
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help):
```

使用 `n` 新增

```bash
Command (? for help): n
Partition number (1-128, default 1): # 新硬盘直接默认
First sector (34-10485726, default = 2048) or {+-}size{KMGTP}: 	#直接默认
Last sector (2048-10485726, default = 10485726) or {+-}size{KMGTP}: +1G	#使用+-的方式
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): 	# 文件系统，默认为linux
Changed type of partition to 'Linux filesystem'

Command (? for help): p		# 查看当前的分区
Disk /dev/sdb: 10485760 sectors, 5.0 GiB
Logical sector size: 512 bytes
Disk identifier (GUID): 6C59F49E-E067-4306-9FBF-3A2517CDA110
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 10485726
Partitions will be aligned on 2048-sector boundaries
Total free space is 8388541 sectors (4.0 GiB)

# 预览创建的新分区
Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem

# 最后记得w写入，否则不会生效
Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!
```

分区创建完毕，我们需要更新分区表。一种方法是重启，另一个则是通过 `partprobe` 处理（`-s` 参数可以看到更多信息）

- 使用 `gdisk` 删除分区

```bash
Command (? for help): d		# 删除分区
Partition number (1-3): 3	# 删除的分区号

Command (? for help): p		# 可以看到已经被删除了，保存即可
Disk /dev/sdb: 10485760 sectors, 5.0 GiB
Logical sector size: 512 bytes
Disk identifier (GUID): 4B3FCDF4-2C44-4179-80E4-B13EB78CF84E
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 10485726
Partitions will be aligned on 2048-sector boundaries
Total free space is 6291389 sectors (3.0 GiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048         2099199   1024.0 MiB  8300  Linux filesystem
   2         2099200         4196351   1024.0 MiB  0700  Microsoft basic data

Command (? for help): w		# 一定要保存
```

> 千万不要修改一个正在使用中的分区，如果要操作必须要先卸载。否则硬盘还是会写入正确的分区信息，但是核心无法更新分区表信息。



- 使用 `fdisk` 

和 `gdisk` 几乎一模一样



#### mkfs 磁盘格式化

分区完毕后当然要对磁盘进行格式化

- XFS 文件系统 `mkfs.xfs`

将前一节分区出来的格式化为 xfs 文件系统，最简单的命令就是 `mkfs.xfs /dev/sdb1` 全部采用默认值。如果有其他额外的处理项目，才需要设置，例如 xfs 可以使用多个数据流，因此 agcount 可以和 CPU 核心搭配

```bash
# 查看cpu数量并设置 agcount 数值
[root@localhost ~]# grep 'processor' /proc/cpuinfo 
processor       : 0
processor       : 1
[root@localhost ~]# mkfs.xfs -f -d agcount=2 /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=2, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

- XFS 文件系统 for RAID

磁盘阵列 （RAID） 就是通过将文件先细分为数个小型的分区区块 （stripe） 之后，然后将众多的 stripes 分别放到磁盘阵列里面的所有磁盘， 所以一个文件是被同时写入到多个磁盘去，当然性能会好一些。

- EXT4 文件系统 mkfs.ext4

- 其他文件系统 `mkfs`

`mkfs`  是综合性指令，使用 `mkfs -t xfs` 就会列出 `mkfs.xfs` 的参数

#### 文件系统校验

- 使用 `xfs_repair` 处理 XFS 文件系统

> [root@study ~]# xfs_repair [-fnd] 设备名称
> 选项与参数：
> -f  ：后面的设备其实是个文件而不是实体设备
> -n  ：单纯检查并不修改文件系统的任何数据 （检查而已）
> -d  ：通常用在单人维护模式下面，针对根目录 （/） 进行检查与修复的动作！很危险！不要随便使用

检查一下上节创建的 /dev/sdb1 的 xfs 文件系统

```bash
[root@localhost ~]# xfs_repair /dev/sdb1
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan and clear agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 0
        - agno = 1
Phase 5 - rebuild AG headers and trees...
        - reset superblock...
Phase 6 - check inode connectivity...
        - resetting contents of realtime bitmap and summary inodes
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify and correct link counts...
done
```

一共七个流程

检查一下系统原有的 /dev/centos/home 文件系统

```bash
[root@localhost ~]# xfs_repair /dev/centos/root
xfs_repair: /dev/centos/root contains a mounted filesystem
xfs_repair: /dev/centos/root contains a mounted and writable filesystem

fatal error -- couldn't initialize XFS library
```

发现要修复的设备已经被挂载，修复时分区不能挂载。需要卸载后再处理

Linux下 如果Linux的根目录出问题，只能进入单人维护或者救援模式，通过 `-d` 来处理了。

- `fsck.ext4` 处理 EXT4 文件系统

`fsck` 是综合指令，针对 EXT4 直接用 `fsck.ext4` 比较妥当

```bash
[root@localhost ~]# fsck.ext4 /dev/sdb2
e2fsck 1.42.9 (28-Dec-2013)
/dev/sdb2: clean, 11/65536 files, 12955/262144 blocks
```

文件系统正常并不会进入检查，挥涕是系统没问题。使用 `-f`会进入强制检查。正常情况下使用此指令可能造成系统危害。

#### 文件系统挂载与卸载

##### mount

- 单一文件系统不应该重复挂载到不同的挂载点
- 单一目录不应该重复挂载多个文件系统
- 作为挂载点的目录应该为空

在 CentOS 7 中，系统会自动读欲挂载分区的 superblock 以判断 filesystem

- /etc/filesystems : 系统制定的测试挂载文件系统类型的优先顺序
- /proc/filesystems : Linux 系统已经载入的文件系统类型

查看 Linux 中支持的文件系统驱动路径，例如 ext4

- /lib/modules/$ (uname -r)/kernel/fs/ext4

**使用 UUID 识别文件系统，并进行挂载**

```bash
[root@localhost ~]# mount UUID="764e398f-7045-4051-b199-6f5c919fac15" /data/xfs
[root@localhost ~]# df /data/xfs -h
文件系统        容量  已用  可用 已用% 挂载点
/dev/sdb1      1014M   33M  982M    4% /data/xfs
```

- 重新挂载根目录与挂载不特定目录

例如根目录出现“只读”情况，需要重新挂载：`mount -o remount,rw,auto /`
重点在于 `-o mount, xx` 参数

- 将目录挂载到另一个目录去

```bash
[root@localhost ~]# mount --bind /var /data/var[root@localhost ~]# ll -id /data/var /var
50331713 drwxr-xr-x. 21 root root 4096 11月 15 15:58 /data/var
50331713 drwxr-xr-x. 21 root root 4096 11月 15 15:58 /var
```

两者链接到同一个 inode, 与硬链接相似。通过`mount --bind` 将目录挂载到其他目录，而不是整块 filesystem

##### umount

卸载的方式，可以下达设备文件名或挂载点

> [root@study ~]# umount [-fn] 设备文件名或挂载点
> 选项与参数：
> -f  ：强制卸载！可用在类似网络文件系统 （NFS） 无法读取到的情况下；
> -l  ：立刻卸载文件系统，比 -f 还强！
> -n  ：不更新 /etc/mtab 情况下卸载。

### 磁盘/文件系统参数修改

修改 Label name 等参数

- mknod

前文说过，在 Linux 下所有的设备都以文件来代表。`ll /dev/sda*`
*mark 太难了不想学

- xfs_admin 修改 XFS 文件系统的 UUID 和 Label name

> ```
> [root@study ~]# xfs_admin [-lu] [-L label] [-U uuid] 设备文件名
> 选项与参数：
> -l  ：列出这个设备的 label name
> -u  ：列出这个设备的 UUID
> -L  ：设置这个设备的 Label name
> -U  ：设置这个设备的 UUID 喔！
> ```

```bash
[root@localhost ~]# xfs_admin -L cyo57_xfs /dev/sdb1
writing all SBs
new label = "cyo57_xfs"
[root@localhost ~]# blkid /dev/sdb1
/dev/sdb1: LABEL="cyo57_xfs" UUID="764e398f-7045-4051-b199-6f5c919fac15" TYPE="xfs" PARTLABEL="Linux filesystem" PARTUUID="6e9fe2df-d235-46e5-b5a3-34b99a582752"
```

可以利用 `uuidgen` 生成新 UUID 来设置 /dev/sdb1 并测试挂载

- `tune2fs` 修改 ext4 的 label name 与 UUID

类似于xfs_admin，参考`man tune2fs`

### 开机挂载

#### /etc/fstab 和 /etc/mtab

系统限制

- 根目录是必须挂载的
- mount point 必须是以创建的目录，遵守FHS
- mount point 在同一时间只能挂载一次
- partition 在同一时间只能挂载一次
- 卸载目录必须先切换到 mount point 之外

其中 /etc/fstab 就是我们利用 mount 指令挂载时，系统将参数写入到此文件中

#### 特殊设备 loop 挂载

- 挂载光盘镜像文件

```bash
[root@localhost ~]# mount -o loop /tmp/CentOS-7.0-1406-x86_64-DVD.iso /data/centos_dvd
[root@localhost ~]# df /data/centos_dvd
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/loop0       4050860 4050860         0 100% /data/centos_dvd

# 其实在CentOS7上，不加-o loop也可以正常挂载
```

- 创建大文件制作 loop 设备文件

制作一个文件然后挂载，就像多了一个分区。使用 `dd` 创建空文件

```bash
[root@localhost testdir]# dd if=/dev/zero of=/~ bs=1M count=512
记录了512+0 的读入
记录了512+0 的写出
536870912字节(537 MB)已复制，0.223703 秒，2.4 GB/秒

# if    是 input file ，输入文件。那个 /dev/zero 是会一直输出 0 的设备！
# of    是 output file ，将一堆零写入到后面接的文件中。
# bs    是每个 block 大小，就像文件系统那样的 block 意义；
# count 则是总共几个 bs 的意思。所以 bs*count 就是这个文件的容量了！
```

- 自动挂载

添加到 /etc/fstab 中，`/srv/loopdev  /data/file  xfs  defaults**,loop**   0 0`

### swap创建

假设系统创建完毕后，发现没有 swap 分区。

- 设置一个 swap partition
- 创建一个swap 文件

#### 使用实体分区创建 swap

1. 分区，通过 `gdisk` 分区作为 swap。由于 `gdisk` 默认将分区id设置为 linux filesystem，还需设置一下 system id
2. 格式化，使用 `mkswap`
3. 将 swap 设备启动，`swapon /dev/xxx`，使用 `-s` 参数查看swap
4. 检查，通过 `free` 和 `swapon -s` 观察用量

#### 使用文件创建swap

1. 创建空文件 `dd if=/dev/zero of=/tmp/swap bs=1M count=512`
2. 格式化，使用 `mkswap`
3. 将 `swap` 设备启动，使用 `swapon`，使用 `-s` 参数查看
4. 讲 `swap` 设备关闭，使用 `swapoff`，设置自动启动（修改 /etc/fstab，`/tmp/swap  swap  swap  defaults  0  0`）

### 文件系统的特殊观察与操作

#### 磁盘空间浪费的问题

之前 EXT2 data block 介绍中了解到一个 block 只能放置一个文件，如果太多小文件就会浪费很多磁盘容量。包括 superblock, inode talbe和其他数据都会浪费磁盘容量。使用`ls -sl` 查看 "total" 列，就是数据所耗的实际 block 数量

#### 利用 parted 进行分区

使用 `gdisk` 或 `fdisk` 分区之前，需要先查询正确到分区表类型才能使用。而 `parted` 是两者同时支持

## 压缩，打包，备份

### 常见压缩命令

Linux 中常用的压缩格式，压缩率xz > bz2 > gz，但相对应压缩所需要的时间也增加了

```
*.Z         compress
*.zip       
*.gz        gzip
*.bz2       bzip2
*.xz        xz
*.tar       tar 打包
*.tar.gz    tar 打包，gzip 压缩
*.tar.bz2   tar 打包，bzip2 压缩
*.tar.xz    tar 打包，xz 压缩
# 因为 bzip2 和 xz 只支持单文件压缩，所以先使用 tar 打包为一个文件
```

#### gzip, zcat/zmore/zless/zgrep

gzip可以解压缩 zip, gz 等。

我们知道`cat`, `more`, `grep`可以用来读取文本，而`zcat`, `zmore`, `zless`, `zgrep` 则是用来读取压缩后的文本文件

#### bzip2, bzcat/bzmore/bzless/bzgrep

gzip 是取代 compress 而诞生的，bzip2 则是取代 gzip 而诞生，提供更高的压缩比。用法和 gzip 几乎相同

#### xz, xzcat/xzmore/xzless/xzgrep

xz 则又是取代 bzip2 而诞生，用法也跟前者几乎一样，详细看 `--help`

#### tar 打包

前文所写的压缩软件虽然也能针对目录进行压缩，但指的是“将目录内的所有文件分别压缩”，而不是直接打包成一个文件并压缩。

`tar` 可以将多个文件打包为一个大文件，然后使用 gz/bz2/xz 等格式进行压缩

##### tar 常用命令

- 压缩：tar -jcv -f filename.tar.bz2 要被压缩的文件或目录名称
  - `-z`: --gzip
  - `-j`: bz2
  - `-J`: xz

- 查询：tar -jtv -f filename.tar.bz2
- 解压缩：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录

示例：备份 /etc 目录 `tar -zpcv -f /root/etc.tar.gz /etc`。`-p` 保留原始权限，`-v` 显示日志，`-c` 创建归档。
`tar` 默认会将文件路径的根目录移除，防止解压时主机数据被覆盖。如果确实需要打包时路径为根目录，参数加上 `-p` 即可。

- 打包某目录，但不包含某些文件

假设我们要打包 /etc, /root 这几个目录，但是不想要 /root/etc* 开头的文件。并保存到 /rppt/system.tar.bz2，（不打包自己，因为自己也 /root）。需要使用到 `--exclude` 的帮助。

```bash
[root@cent7 ~]# tar -jcpf /root/systembk.tar.bz2 \
--exclude=/root/etc* \
--exclude=/root/systembk.tar.bz2 /etc /root
```

- 备份某个时刻之后的文件

某些情况下只想备份新文件，不想要旧的。此时 `--newer-mtime` 选项，但如果只使用 `--newer` 则标识包含 mtime 与 ctime。

```bash
# 用 find 找出 比 /etc/passwd 更新的文件
[root@cent7 ~]# find /etc -newer /etc/passwd
```

## 进程管理

