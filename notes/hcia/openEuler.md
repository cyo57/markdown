# openEuler


开源软件(open source software)的概念首次正式提出于
- 1998.02

开源软件的概念提出是在
- 1998年2月3日

在MBR分区方案中，主引I导程序占用的字节数是多少？
- 446

在openEuler 系统中定义了一个变量ORDER，并为其赋值为130，以下哪个命令中调用了该变量?
- $ORDER
- "$ORDER"

首次正式提出开源软件(open source software)的概念的组织是
- 开放源代码促进会(OpenSource Initiative，OSl)

openEuler社区的开发活动是以下面哪种组织来开展的
- SIG

Linux系统中，nmcli命令可以配置和添加静态路由，如果采用交互式，命令"save persistent"代表什么含义？
- 将当前配置的网络设置（包括静态路由）永久保存到系统中

openEuler系统中，仅能使用route命令对系统中的路由表进行管理
- True

openEuler 版本发布
- 2019发布
- LTS - 4year
- community - 6month

以下哪条命令可以查看文件/etc/passwd来自于哪个软件包?
- rpm -qf /etc/passwd

以下可以进行进程绑核的工具是哪一项？
- numactl

关于命令docker Commit -a “huawei.com" -m "redis images" f4603f1f918at arm64v8/centos:7，以下哪项描述是错误的
- 该命令功能是根据镜像ID创建一个新的镜像
> 根据**容器**ID创建

在OpenEuler中，timedatect命令修改日期格式正确的是?
- YYYY-mm-DD

在MBR分区方案中，分区表占用的字节长度为什么？
- 分区表 64
- 启动代码 446
- MBR 签名 2

使用crontab命令制定周期性计划任务时，以下哪些选项中的值可以用来表示分钟
- 0-59

vim如何把35-45行之间的内容保存到b.txt
- :35,45w b.txt

## 填空

find
- find path -name pattarn
- 注意 find 正确的参数只有一个 `-`

find 查找30天以内的文件
- find . -type f -mtime -30
- mtime 修改过
- atime 访问过
- ctime 状态改变过

find 查找30天以外的文件 并删除
- find . -type f -mtime +30 -delete

查看所有一次性任务 (at)
- at -l

parted 工具保存并退出
- quit

yum 更新全部软件包
- yum updatedddd
- 注意区别于 apt 和 apt-get

通过源码安装软件的步骤
- 配置、编译、安装

创建目录下 metadata 和软件包的依赖
- createrepo --database/software

gpasswd 批量添加成员
- gpasswd -M user1,user2 grouptttttt

gpasswd 添加成员
- gpasswd -a

dnf 添加仓库源
- dnf config-mamager URL

systemd 查看运行中的服务
- systemctl list-units --type service

chage 查看用户过期时间
- chage -l root

Shell比较运算符
- `-eq` : 等于
- `-ne` : 不等于
- `-gt` : 大于
- `-lt` : 小于
- `-ge` : 大于等于
- `-le` : 小于等于

dnf 查看包组包含的软件
- dnf group info XXX

usermod 迁移用户目录
- usermod -md newpath user

