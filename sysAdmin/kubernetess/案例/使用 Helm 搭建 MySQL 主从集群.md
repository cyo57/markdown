---
tags:
  - k8s
  - mysql
date: "240526"
---

## 安装 Helm

### 配置 Helm
参考 [1.2 Dashboard](1.2%20Dashboard.md)

- 配置官方仓库
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```


## 安装 MySQL
### 修改 MySQL 镜像配置文件
- 拉取并修改配置文件
```bash
# 拉取 MySQL
mkdir -p /k8s/mysql && cd /k8s/mysql
helm pull bitnami/mysql --version 9.12.1
tar -xvf mysql-9.12.1.tgz
cp mysql/values.yaml ./values-test.yaml
```

- values-test.yaml
```yaml
#镜像修改
image:
  registry: registry.cn-hangzhou.aliyuncs.com
  repository: senfel/mysql
  tag: 8.0.34-debian-11-r31
#架构改为集群复制
architecture: replication
#root密码 主从复制密码
auth:
  rootPassword: "123456root"

#主节点
primary:
  configuration: |-
    [mysqld]
    default_authentication_plugin=caching_sha2_password   ## 加密方式

  persistence: ## 储存方式
    storageClass: "nfs-storage"
#从节点副本数
secondary:
  replicaCount: 2
#从节点
secondary:
  configuration: |-
    [mysqld]
    default_authentication_plugin=caching_sha2_password  ## 加密方式
    read_only=1 ##限定普通用户只读
    super_read_only=on ##限定root只读

  persistence: ##储存方式
    storageClass: "nfs-storage"
```

### 安装 MySQL 集群

```bash
# 创建名称空间
kubectl create ns mysql-cluster

# 安装 MySQL 集群
helm install mysql-cluster -n mysql-cluster mysql -f values-test.yaml

# 查看部署的 MySQL 集群
kubectl get svc,pods -n mysql-cluster

# 查看 pvc
kubectl get pvc -n mysql-cluster

# 查看 pv
kubectl get pv | grep mysql
```

#### 验证 MySQL 集群

- 创建临时容器
```bash
kubectl run mysql-cluster-client --rm --tty -i --restart=‘Never’ --image registry.cn-hangzhou.aliyuncs.com/senfel/mysql:8.0.34-debian-11-r31 --namespace mysql-cluster --env MYSQL_ROOT_PASSWORD=$MYSQL_ROOT_PASSWORD --command – bash
```

- MySQL Master
```
# 登录节点
mysql -h mysql-cluster-primary.mysql-cluster.svc.cluster.local -uroot -p

# 查看状态
show master status\G
```

- MySQL Slave
```bash
mysql -h mysql-cluster-secondary.mysql-cluster.svc.cluster.local -uroot -p
show slave status\G;
```

## 开放端口

### ingress-nginx

> ingress-nginx是K8S开放端口的工具，可以直接将端口映射到集群外部。

- values.yaml
```yaml
#格式 "ns":"svc:port"
tcp: {
  "56379":"redis-cluster/redis-cluster:6379",
  "53306": "mysql-cluster/mysql-cluster-primary:3306",
  "54406": "mysql-cluster/mysql-cluster-secondary:3306"
}
```

- 升级 releas
```bash
helm upgrade ingress-nginx -n ingress-nginx .
```

- 验证端口开放
```bash
netstat -nplt | grep 06
```

- 最终的 nginx.conf
```bash
kubectl get pods -n ingress-nginx
```