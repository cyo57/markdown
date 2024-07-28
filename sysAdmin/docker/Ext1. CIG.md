---
tags:
  - docker
---
重量级监控系统
CIG (CAdvisor + InfluxDB + Granfana)

- CAdvisor 监控
> 包括内存、CPU、网络、磁盘等，同时提供一个Web页面查看实时状态，默认存储两分钟数据，只针对单台物理机。
> 但CAdvisor提供了很多接口，支持将数据发往多种数据库

- InfluxDB 存储
> CAdvisor是Go语言开发的开源分布式时序、事件、指标数据库，且无需依赖

- Granfana 图标
> 提供可视化

## docker stats

可以很方便的看到当前宿主机的CPU、内存、网络等数据
数据为实时，不支持地方存储、健康指标、预警等功能