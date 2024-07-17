---
tags:
  - openstack
aliases:
  - 虚拟网络
---
> [!note]
> OpenStack VPNaaS（Virtual Private Network as a Service）是Neutron的一个扩展，为OpenStack提供虚拟专用网络服务。它允许租户创建和管理IPsec VPN连接，以安全地连接到他们的虚拟机和其他网络资源。

## 安装

root@controller
```bash
[root@controller ~]# iaas-install-fwaas-and-vpnaas.sh
```
## 配置

### 网络拓补

单个集群中的不同租户

### 创建路由网络

- 使用脚本创建路由网络
route-net-build.sh
```shell
#/bin/bash

source /etc/keystone/admin-openrc.sh
#创建vxlan外网网络ext-net
openstack network create --external --share ext-net
openstack subnet create --subnet-range 100.0.0.0/24 --gateway 100.0.0.1 --network ext-net ext-subnet

#admin租户创建路由网络
#创建vxlan内网网络net1
openstack network create net1
openstack subnet create --subnet-range 100.0.1.0/24 --gateway 100.0.1.1 --network net1 net1-sub
#创建路由route1，网关100.0.0.11，添加内网net1
openstack router create route1
openstack router set --external-gateway ext-net --fixed-ip subnet=ext-subnet,ip-address=100.0.0.11 route1
openstack router add subnet route1 net1-sub
#---------------------------------------------------------------
#demo租户创建路由网络
source /etc/keystone/demo-openrc.sh
#创建vxlan内网网络net2
openstack network create net2
openstack subnet create --subnet-range 100.0.2.0/24 --gateway 100.0.2.1 --network net2 net2-sub
#创建路由route2，网关100.0.0.22，添加内网net2
openstack router create route2 
#---------------------------------------------------------------
source /etc/keystone/admin-openrc.sh
openstack router add subnet route2 net2-sub
openstack router set --external-gateway ext-net --fixed-ip subnet=ext-subnet,ip-address=100.0.0.22 route2
```

### 构建VPN连接

- 在admin项目创建vpn连接，--peer-address为demo项目的路由route2网关100.0.0.22
```bash
[root@controller ~]# source /etc/keystone/admin-openrc.sh
[root@controller ~]# openstack vpn ike policy create ikepolicy1
[root@controller ~]# openstack vpn ipsec policy create ipsecpolicy1
[root@controller ~]# openstack vpn service create --router route1 --subnet net1-sub vpn1
[root@controller ~]# openstack vpn ipsec site connection create vpnconnectiona --vpnservice vpn1 --ikepolicy ikepolicy1  --ipsecpolicy ipsecpolicy1 --peer-address 100.0.0.22 --peer-id 100.0.0.22 --peer-cidr 100.0.2.0/24 --psk secret
```
## 使用