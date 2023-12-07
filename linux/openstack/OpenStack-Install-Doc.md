# OpenStack Install Doc

## 准备工作

### Controller

硬盘 * 1
isaa.iso

```bash
# 修改网卡配置
[root@controller ~]# /etc/sysconfig/ifcfg-ensxx


# 修改主机名和hosts (修改第一网卡host only 即可)
[root@controller ~]# hostnamectl set-hostname xxx
[root@controller ~]# vi /etc/hosts


# 关闭防火墙和SELinux
[root@controller ~]# vi /etc/selinux/config
[root@controller ~]# systemctl disable firewalld


# 配置yum local源
[root@controller ~]# vi /etc/yum.repos.d/xxx.repo

    [centos]
    name = centos
    baseurl=file:///opt/centos
    gpgcheck=0
    enabled=1
    [iaas] # 同理

#配置完成后清空yum缓存并检查
[root@controller ~]# yum clean all & yum repolist


# 安装常用软件（双节点）
[root@controller ~]# yum install vim bash-completion openstack-iaas


# 修改 openstack 环境变量配置
[root@controller ~]# vim /etc/openstack/openrc.sh
	# 使用正则表达式 %s/str1/str2/g 批量替换
	

# 拷贝出 compute 节点并为其添加硬盘，修改网卡、主机名配置


# 配置ftp服务 作为compute节点yum源（可选）
	# baseurl=ftp://controller/centos
[root@controller ~]# yum install vsftpd
[root@controller ~]# echo "anon_root=/opt" >> /etc/vsftpd/vsftpd.conf
[root@controller ~]# systemctl enabled --now vsftpd
```



## 部署 issa

确保双节点都安装了 `openstack-issa`

- Controller 节点

`vim /etc/openstack/openrc.sh`

| name           | value            |
| -------------- | ---------------- |
| INTERFACE_IP   | **对应节点**的IP |
| INTERFACE_NAME | 外部网卡名       |
| BLOCK_DISK     | sdb1             |
| OBJECT_DISK    | sdb2             |
| SHARE_DISK     | sdb3             |

- Compute 节点

```bash
scp root@controller:/etc/openstack/openrc.sh /etc/openstack # 从控制节点拷贝变量配置文件
```

- 所有节点

```bash
source /etc/openstack/openrc.sh
```

### 执行安装脚本

- 所有节点

`iaas-pre-host.sh`, 安装后**需要重启**

- Controller 节点

```bash
iaas-install-mysql.sh # 数据库
iaas-install-keystone.sh # 验证
iaas-install-glance.sh # 镜像管理
iaas-install-placement.sh # 管理和分配云计算资源
iaas-install-nova-controller.sh # 虚拟机创建、调度
iaas-install-neutron-controller.sh # 网络服务
iaas-install-dashboard.sh # 面板

# 高级服务
iaas-install-cinder-controller.sh # 块模块
iaas-install-swift-controller.sh # 对象存储模块
```

- Compute 节点

```bash
iaas-install-nova-compute.sh
iaas-install-neutron-compute.sh

# 高级服务
iaas-install-cinder-compute.sh
iaas-install-swift-compute.sh
```

## 通过面板访问

### 面板创建云主机

#### 网络和子网

- 创建外部网络 (管理员)

  > 如果是VMWare，需要和VMnet8保持一致

- 创建内部网络 (项目)

  > 网段任意

##### 命令行查看

```bash
source /etc/keystone/admin-openrc.sh
neutron net-list
neutron subnet-list
openstack network list
openstack subnet list
```

- 创建路由 (项目)

  > 将路由链接到外部网络，并添加接口连接到内部网络的子网

#### 上传镜像

- 创建云主机类型

分配CPU，内存，硬盘等等

- 选择镜像
- 选择云主机类型
- 网络选择内网

#### 分配浮动IP

> 使外网可访问内网

资源池选择外网

#### 设置安全组规则

启用 SSH 端口和 ICMP

为云主机创建卷

## 通过命令访问

```bash
# 将keystone密码保存到环境变量
[root@controller ~]# source /etc/keystone/admin-openrc.sh
```

### 命令创建云主机

#### 创建网络、子网、路由器

```bash
# 查看网络列表
openstack network list

# 创建内网 和 内网子网
openstack network create <int-name>
openstack subnet create \
--network int-kang1 int-kang1-sub \
--subnet-range 10.2.2.0/24 \
--gateway 10.2.2.1 \
--allocation-pool start=10.2.2.10,end=10.2.2.100 \
--dns-nameserver 114.114.114.114

# 创建外网 和 外网子网
openstack network create <ext-name> \
--project admin \
--provider-network-type vlan \
--provider-physical-network provider \
--provider-segment 120 \ # VLAN ID
--share \
--external

# 创建路由,连接外网和内网
openstack router create <router-name>
openstack router set --external-gateway [ext-net] <router-name>
openstack router add subnet <router-name> <int-net-name>

# 为实例分配路由器接口
openstack server add port cirros-test \
--port-id <port_id_of_int-test>
```

#### 上传镜像
```bash
openstack image create \
--disk-format qcow2 \
--container-format bare \
--public \
--file /path/to/image \
<name>
```

#### 创建实例类型

```bash
openstack flavor list # 创建实例类型
 openstack flavor create flavor-kang1 \ # 名称
 --ram 2048 \ # 内存大小MB
 --disk 50 \ # 磁盘大小GB
 --vcpus 2 # cpu内核
```

#### 创建实例

```bash
openstack server create \
--image centos7.9 \ # 镜像
--flavor flavor-kang1 \ # 实例类型
--network int-kang1 \ # 网络
centos7.9-kang # 名称
```

#### 创建浮动IP

```bash
openstack floating ip list
openstack floating ip create ext-kang1
openstack floating ip list
openstack server add floating ip centos7.9-kang 192.168.200.111
```


## 配置模块

### neutron

```bash
# 配置文件路径
/etc/neutron/plugins/ml2
```

