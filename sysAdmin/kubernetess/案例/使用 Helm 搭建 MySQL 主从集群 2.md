---
tags:
  - k8s
  - mysql
date: "240526"
---

## 安装 Helm

### 配置 Helm

Helm 安装参考 [1.2 Dashboard](1.2%20Dashboard.md)

- 配置官方仓库
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```


## 安装 MySQL
### 修改 MySQL 镜像配置文件

- 安装 MySQL
```bash
helm install  mysql bitnami/mysql
```

- MySQL config.yaml
```yaml
namespaceOverride: "prod"
architecture: "replication"
auth:
  createDatabase: true
  database: "prod_db"
  username: "java4ye"
  existingSecret: "mysql-secret"
primary:
  service:
    type: NodePort
  persistence:
    storageClass: "nfs-mysql"
    size: "10Gi"
    accessModes:
    - "ReadWriteMany"
  extraEnvVars:
   - name: TZ
     value: "Asia/Shanghai"
secondary:
  name: slave
  extraEnvVars:
   - name: TZ
     value: "Asia/Shanghai"
  service:
    type: NodePort
  replicaCount: 1
  persistence:
    storageClass: "nfs-mysql-slave"
    size: "10Gi"
    accessModes:
    - "ReadWriteMany"
    resources:
      limits:
        cpu: 1000m
        memory: 1Gi
      requests:
        cpu: 500m
        memory: 512Mi
```
### 搭建 NFS 服务

```bash
#master节点安装nfs
yum -y install nfs-utils

#创建nfs目录
mkdir -p /root/nfs

#修改权限
chmod -R 777 /root/nfs

#编辑export文件
vim /etc/exports
/root/nfs/data *(rw,no_root_squash,sync)

#配置生效
exportfs -r
#查看生效
exportfs

#启动rpcbind、nfs服务
systemctl restart rpcbind && systemctl enable rpcbind
systemctl restart nfs-server && systemctl enable nfs-server

#查看 RPC 服务的注册状况
rpcinfo -p localhost

#showmount测试
showmount -e 

```

- pv mysql-master config.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
  namespace: prod
  labels:
    storage: pv
spec:
  storageClassName: "nfs-mysql"
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 10Gi
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    path: /root/nfs/mysql
    server: 10.0.8.8
```

- pv mysql-slave config.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-slave-pv
  namespace: prod
  labels:
    storage: pv
spec:
  storageClassName: "nfs-mysql-slave"
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 10Gi
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    path: /root/nfs/mysql-slave
    server: 10.0.8.8
```

- pvc mysql-master config.yaml
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  namespace: prod
spec:
  storageClassName: "nfs-mysql"
  accessModes:
    - ReadWriteMany
  volumeName: "mysql-pv"
  resources:
    requests:
      storage: 10Gi
```

- pvc mysql-slave config.yaml
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  namespace: prod
spec:
  storageClassName: "nfs-mysql-slave"
  accessModes:
    - ReadWriteMany
  volumeName: "mysql-salve-pv"
  resources:
    requests:
      storage: 10Gi
```

- secret
```bash
echo -n '000000' | base64
```
- secret yaml
```yaml
apiVersion: v1
kind: Secret
metadata: 
  name: mysql-secret
  namespace: prod
type: Opaque
data:  
  mysql-root-password: ***
  mysql-password: ***
  mysql-replication-password: ***
```

### 删除 MySQL

- 卸载 MySQL
```bash
helm uninstall mysql
```

- 指定配置文件启动 MySQL
```bash
helm install mysql bitnami/mysql --namespace prod -f config.yaml
```

- 获取详细信息
```bash
helm get all mysql --namespace prod
```

- port-forward
```bash
kubectl port-forward --address 0.0.0.0 service/mysql-primary  8006:3306 -n prod

```