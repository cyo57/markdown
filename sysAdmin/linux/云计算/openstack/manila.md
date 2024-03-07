---
tags:
  - "#openstack"
aliases:
  - 共享文件
  - 文件共享
---
> [!notes]
> OpenStack的共享文件系统服务——Manila。 Manila 源自 Cinder 项目，在 [OpenStack](https://wiki.openstack.org/wiki/OpenStack "开放堆栈") 中为共享或分布式文件系统提供规范的存储配置控制平面，类似于 Cinder 为块存储提供规范的控制平面。

## 使用

### 创建文件共享类型

```bash
manila type-create default_share_type False
```