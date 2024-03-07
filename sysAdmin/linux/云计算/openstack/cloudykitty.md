---
tags:
  - "#openstack"
aliases:
  - 计费系统
---
> [!notes]
>**Ceilometer**是一个计量服务，负责收集和存储来自OpenStack各个组件的资源使用数据。这些数据包括CPU、内存、网络流量、存储空间等。Ceilometer可以将数据存储在本地数据库中，也可以将其导出到其他存储系统，例如Gnocchi。
>
>**CloudKitty**是一个计费服务，负责根据资源使用情况生成账单。CloudKitty可以从Ceilometer中获取资源使用数据，并根据预定义的计费策略生成账单。CloudKitty支持多种计费模式，例如按需计费、预付费和订阅计费。

## 安装

- 检查环境变量
/etc/openstack/openrc.sh
```bash
CLOUDKITTY_DBPASS=000000
CLOUDKITTY_PASS=000000
```

- 安装服务
```bash
[root@controller ~]# iaas-install-cloudkitty.sh
```

## 使用

### 类型规格费用

- 创建服务
创建云主机服务 instance_test，创建service服务
```bash
openstack rating hashmap service create instance_test
```