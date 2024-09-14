---
tags:
  - "#ansible"
  - "#openstack"
---
## 目标


## 规划

## 实施

### 系统环境

- 防火墙、SELinux、IP、hosts
- 配置YUM
- 安装软件包
```bash
[root@ansible ~]# yum install -y vim net-tools bash-completion
yum install -y ansible
```
- 配置ssh密钥对
```bash
[root@ansible ~]# ssh-keygen
[root@ansible ~]# ssh-copy-id controller
[root@ansible ~]# ssh-copy-id compute
```

### Ansible 角色

- 为 ansible 创建目录和配置文件
> OpenStack平台包含多个步骤，拆分为多个roles更易于编写和阅读。例如
> 安装OpenStack需要init, mariadb, keystone, glance, placement
> nova-controller, nove-compute, neutron-controller, neutron-compute
> cinder-controller, cinder-compute, swift-controller, swift-compute, heat
> 这些roles来完成，可以通过花括号批量创建[[路径与目录]]

```bash
# 目录创建
mkdir /opt/openstack_ansible

# 角色目录，花括号内不能有空格
mkdir -p /opt/openstack_ansible/roles/{init,mariadb,keystone,glance,placement,nova-controller,nova-compute,neutron-controller,neutron-compute,cinder-controller,cinder-compute,swift-controller,swift-compute,heat}

# group_vars，存放变量声明文件all
mkdir group_vars
touch group_vars/all

# 安装入口文件
cd /opt/openstack_ansible
touch install_openstack.yaml
```
接下来对每一个role文件进行编写

### Playbook

在编写playbook前，需要做一些基础工作，将chinaskills_cloud_iaas.iso和centos7.iso上传到ansible并挂在为ftp源以供其他节点访问，配置可参考[[old.1-Install-Doc]]
```bash
[root@ansible ~]# yum install -y vsftpd
# [root@ansible ~]# vim /etc/vsftpd/vsftpd.conf
[root@ansible ~]# echo anon_root=/opt >> /etc/vsftpd/vsftpd.conf
[root@ansible ~]# systemctl enabled --now vsftpd
```

- role init
> 部署controller和compute的基础环境，配置yum、安装openstack-iaas、安装iaas-pre-host等

/roles/init/tasks
```yaml
- name: move repos
  shell: mv /etc/yum.repos.d/* /media
- name: create local.repo
  copy: src=local.repo dest=/etc/yum.repos.d/
- name: install openstack-iaas    
  yum: name=openstack-iaas state=present
- name: openrc.sh
  template: src=openrc.sh.j2 dest=/etc/openstack/openrc.sh
- name: install pre-host
  shell: iaas-pre-host.sh
```

copy模块所需的文件放置在与 `tasks` 目录同级的 `files` 目录下
template模块所需的文件放置在与 `tasks` 目录同级的 `templates` 目录下

local.repo
```bash
[centosftp]
name=centosftp
baseurl=ftp://ansible/centos
gpgcheck=0
enabled=1

[iaasftp]
name=centosftp
baseurl=ftp://ansible/iaas-repo
gpgcheck=0
enabled=1
```

openrc.sh.j2
```bash
#--------------------system Config--------------------##
#Controller Server Manager IP. example:x.x.x.x
HOST_IP={{controller_ip}}

#Controller HOST Password. example:000000 
HOST_PASS={{PASSWD}}

#Controller Server hostname. example:controller
HOST_NAME={{controller_name}}

#Compute Node Manager IP. example:x.x.x.x
HOST_IP_NODE={{compute_ip}}

#Compute HOST Password. example:000000 
HOST_PASS_NODE={{PASSWD}}

#Compute Node hostname. example:compute
HOST_NAME_NODE={{compute_name}}

#--------------------Chrony Config-------------------##
#Controller network segment IP.  example:x.x.0.0/16(x.x.x.0/24)
network_segment_IP=192.168.100.0/24

#--------------------Rabbit Config ------------------##
#user for rabbit. example:openstack
RABBIT_USER=openstack

#Password for rabbit user .example:000000
RABBIT_PASS={{PASSWD}}

#--------------------MySQL Config---------------------##
#Password for MySQL root user . exmaple:000000
DB_PASS={{PASSWD}}

#--------------------Keystone Config------------------##
#Password for Keystore admin user. exmaple:000000
DOMAIN_NAME=demo
ADMIN_PASS={{PASSWD}}
DEMO_PASS={{PASSWD}}

#Password for Mysql keystore user. exmaple:000000
KEYSTONE_DBPASS={{PASSWD}}

#--------------------Glance Config--------------------##
#Password for Mysql glance user. exmaple:000000
GLANCE_DBPASS={{PASSWD}}

#Password for Keystore glance user. exmaple:000000
GLANCE_PASS={{PASSWD}}

#--------------------Placement Config----------------------##
#Password for Mysql placement user. exmaple:000000
PLACEMENT_DBPASS={{PASSWD}}

#Password for Keystore placement user. exmaple:000000
PLACEMENT_PASS={{PASSWD}}

#--------------------Nova Config----------------------##
#Password for Mysql nova user. exmaple:000000
NOVA_DBPASS={{PASSWD}}

#Password for Keystore nova user. exmaple:000000
NOVA_PASS={{PASSWD}}

#--------------------Neutron Config-------------------##
#Password for Mysql neutron user. exmaple:000000
NEUTRON_DBPASS={{PASSWD}}

#Password for Keystore neutron user. exmaple:000000
NEUTRON_PASS={{PASSWD}}

#metadata secret for neutron. exmaple:000000
METADATA_SECRET={{PASSWD}}

#External Network Interface. example:eth1
INTERFACE_NAME={{External_Network}}

#External Network The Physical Adapter. example:provider
Physical_NAME={{Physical_NAME}}

#First Vlan ID in VLAN RANGE for VLAN Network. exmaple:101
minvlan=101

#Last Vlan ID in VLAN RANGE for VLAN Network. example:200
maxvlan=200

#--------------------Cinder Config--------------------##
#Password for Mysql cinder user. exmaple:000000
CINDER_DBPASS={{PASSWD}}

#Password for Keystore cinder user. exmaple:000000
CINDER_PASS={{PASSWD}}

#Cinder Block Disk. example:md126p3
BLOCK_DISK={{cinder_disk}}

#--------------------Swift Config---------------------##
#Password for Keystore swift user. exmaple:000000
SWIFT_PASS={{PASSWD}}

#The NODE Object Disk for Swift. example:md126p4.
OBJECT_DISK={{swift_disk}}

#The NODE IP for Swift Storage Network. example:x.x.x.x.
STORAGE_LOCAL_NET_IP={{compute_ip}}

#--------------------Trove Config----------------------##
#Password for Mysql trove user. exmaple:000000
TROVE_DBPASS={{PASSWD}}

#Password for Keystore trove user. exmaple:000000
TROVE_PASS={{PASSWD}}

#--------------------Heat Config----------------------##
#Password for Mysql heat user. exmaple:000000
HEAT_DBPASS={{PASSWD}}

#Password for Keystore heat user. exmaple:000000
HEAT_PASS={{PASSWD}}

#--------------------Ceilometer Config----------------##
#Password for Gnocchi ceilometer user. exmaple:000000
CEILOMETER_DBPASS={{PASSWD}}

#Password for Keystore ceilometer user. exmaple:000000
CEILOMETER_PASS={{PASSWD}}

#--------------------AODH Config----------------##
#Password for Mysql AODH user. exmaple:000000
AODH_DBPASS={{PASSWD}}

#Password for Keystore AODH user. exmaple:000000
AODH_PASS={{PASSWD}}

#--------------------ZUN Config----------------##
#Password for Mysql ZUN user. exmaple:000000
ZUN_DBPASS={{PASSWD}}

#Password for Keystore ZUN user. exmaple:000000
ZUN_PASS={{PASSWD}}

#Password for Keystore KURYR user. exmaple:000000
KURYR_PASS={{PASSWD}}

#--------------------OCTAVIA Config----------------##
#Password for Mysql OCTAVIA user. exmaple:000000
OCTAVIA_DBPASS={{PASSWD}}

#Password for Keystore OCTAVIA user. exmaple:000000
OCTAVIA_PASS={{PASSWD}}

#--------------------Manila Config----------------##
#Password for Mysql Manila user. exmaple:000000
MANILA_DBPASS={{PASSWD}}

#Password for Keystore Manila user. exmaple:000000
MANILA_PASS={{PASSWD}}

#The NODE Object Disk for Manila. example:md126p5.
SHARE_DISK={{manila_disk}}

#--------------------Cloudkitty Config----------------##
#Password for Mysql Cloudkitty user. exmaple:000000
CLOUDKITTY_DBPASS={{PASSWD}}

#Password for Keystore Cloudkitty user. exmaple:000000
CLOUDKITTY_PASS={{PASSWD}}

#--------------------Barbican Config----------------##
#Password for Mysql Barbican user. exmaple:000000
BARBICAN_DBPASS={{PASSWD}}

#Password for Keystore Barbican user. exmaple:000000
BARBICAN_PASS={{PASSWD}}
```

- 在 `/group_vars/all` 文件中保存变量\
all
```bash
controller_ip: 192.168.100.10
controller_name: controller
compute_ip: 192.168.100.20
compute_name: compute
PASSWD: '000000'
cinder_disk: sdb
swift_disk: sdc
network_segment_IP: 192.168.100.0
External_Network: ens34
Physical_NAME: provider
STORAGE_LOCAL_NET_IP: 192.168.100.20
```

- 其它 role 
其他角色执行安装openstack的脚本

#### 执行 Playbook

运行前做最后的准备工作
- 修改hosts和 `/etc/ansible/hosts`

hosts
```bash
# 同系统环境
```
/etc/ansible/hosts
```bash
# 在文件末尾追加执行剧本的目标主机
[controller]
controller
[compute]
compute
```

- 入口文件，包含调用roles的顺序，和主机所需调用roles的对应关系
install_openstack.yaml
```yaml
- hosts: controller
  remote_user: root

  roles:
    - init
    - mariadb
    - keystone
    - glance
    - placement
    - nova-controller
    - neutron-controller
    - dashboard
    - cinder-controller
    - swift-controller
    - heat

- hosts: compute
  remote_user: root

  roles:
    - init
    - nova-compute
    - neutron-compute
    - cinder-compute
    - swift-compute
```

- 各个role的命令

```bash
```

- 运行剧本
```bash
ansible-playbook install_openstack.yaml
# ansible-playbook install_openstack.yaml -v


# 可编辑配置文件修改日志输出位置
[defaults] 
log_path=/path/to/logfile
```
## review
