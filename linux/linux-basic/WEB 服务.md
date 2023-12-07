# web服务

## DNS 服务器

```bash
yum install bind
systemctl enable named
```

- 修改域名配置文件
```bash
# 查看DNS配置文件目录
rpm -lq bind

vim /etc/named.conf # DNS 服务器属性
vim /etc/named.rfc1912.zones # 区域属性

zone "www.aaa.com" {
    type master;
    file "www.aaa.com.zone";
    allow-update {none;};
}
```

- 添加DNS解析配置文件
```bash
vim /var/named/www.aaa.com.zone

```