
## RabbitMQ 集群部署

### 规划

| **IP** | **主机名** | **节点** |
| ---- | ---- | ---- |
| vmnet | rabbitmq1 | RabbitMQ 磁盘节点 |
| vmnet | rabbitmq2 | RabbitMQ 内存节点 |
| vmnet | rabbitmq3 | RabbitMQ 内存节点 |

## 实施

### 系统环境

- 主机名
- hosts
- firewalld和SELinux
- yum 源
- 安装并启动 rabbitMQ
```bash
tar -zxvf rabbitmq-repo.tar.gz -C /opt
vim /etc/yum.repo.d/local.repo
......

yum install -y rabbitmq-server
systemctl enable --now rabbitmq-server
```
- 启用图形界面访问
```bash
rabbitmq-plugins enable rabbitmq_management
systemctl restart rabbitmq-server

# 图形化访问端口为15672，使用命令查看端口情况
# 用户名密码：guest
netstat -ntpl
```

### 配置集群

- 配置节点间的通信
> RabbitMQ集群依赖于Erlang集群，因此在构建RabbitMQ集群之前必须先建立一个Erlang集群。Erlang集群的各个节点通过"magic cookie"来实现，每个节点都需要保持相同的`.erlang.cookie`文件。该cookie文件通常存放在`/var/lib/rabbitmq/`路径下，并设置为400权限。确保各个节点的cookie文件一致是必要的，否则节点之间将无法正常通信。

```bash
# 将 rabbitmq1 节点的 .erlang.cookie 复制到其他节点
scp /var/lib/rabbitmq/.erlang.cookie root@rabbitmq2:/var/lib/rabbitmq/
scp /var/lib/rabbitmq/.erlang.cookie root@rabbitmq3:/var/lib/rabbitmq/

# 修改该文件的所属
chown rabbitmq:rabbitmq .erlang.cookie
```

- 配置节点加入集群
```bash
# 将2/3节点作为 RAM节点 加入到RabbitMQ
rabbitmqctl stop_app
rabbitmqctl join_cluster --ram rabbit@rabbitmq1
rabbitmqctl start_app
```
> 默认 rabbitMQ 启动后是磁盘节点，在 cluster 命令下，rabbitMQ2 和 rabbitMQ3 是**内存节点**，而rabbitMQ1**是磁盘节点**
> 如果希望 rabbitMQ2/3 也是磁盘节点，去掉`--ram`即可

- 修改节点类型（可选）
```bash
# 修改前必须停止
rabbitmqctl stop_app
rabbitmqctl change_cluster_node_type disc(ram),
rabbitmqctl start_app
```

- 配置 RAM 节点启动界面
```bash
# 在2/3启动
rabbitmq-plugins enable rabbitmq_management
systemctl restart rabbitmq-server
# 访问 web dashboard
```

- 查看插件启用情况
```bash
rabbitmq-plugins list
```
- 启动/关闭 监控管理器
```bash
rabbitmq-plugins enable rabbitmq_management
rabbitmq-plugins disable rabbitmq_management
```

- 队列操作
```bash
# 查看所有队列
rabbitmqctl list_queues

# 清空队列
rabbitmqctl reset

# 查看用户
rabbitmqctl list_users

# 查看状态
rabbitmqctl status

# 查看集群状态
rabbitmqctl cluster_status
```

- 用户管理
```bash
# 新增、删除、修改密码
rabbitmqctl add_user <username> <password>
rabbitmqctl delete_user <username>
rabbitmqctl change_password <username> <new_password>
```

- 角色管理
```bash
rabbitmqctl set_user_tags <username> administrator monitoring policymaker management
```

- 权限管理
```bash
rabbitmqctl set+permissions -p VHostPath  admin  ConfP  WriteP  ReadP

# 查询权限命令
rabbitmqctl  list_permissions  [-p VHostPath]

# 指定用户权限
rabbitmqctl list_user_permissions admin

# 清除用户权限
rabbitmqctl clear_permissions [-p VHostPath] admin
```