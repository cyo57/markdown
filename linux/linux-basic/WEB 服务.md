# 域名服务器

## named

### 概念

域名作为一个倒装的树，从根节点开始解析，直到叶子节点
- . - 根域
- .com .cn .prg .net - 顶级域
- aaa.com aaa.com.cn aaa.cn - 二级域名
- bbb.aaa.com ccc.aaa.com - 子域

详细的请求顺序大致为：

![[12979420-8912851ab578a754.webp]]

1. 客户端host标识
2. 客户缓存
3. 服务器区域设定
4. 服务器缓存
5. 其他服务器

### 安装 DNS 服务端

install bind bind-chroot bind-utils

### 修改配置文件

- 实用命令
```bash
# 检查配置文件是否正确
named-checkconf <file>
named-checkzone <name> <file>
```

- 修改 `/etc/named.conf` 监听为 any
- 修改 `/etc/named.rfc1912.zones`
```bash
zone "aaa.com" IN {
	type master;
	file "1.zone";
	allow-update { none; };
};
```

- 创建 `/var/named/1.zone`
可以从 named.localhost 复制模板
```bash
$TTL 1D
@       IN SOA  @ rname.invalid. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      www.aaa.com.
www     A       192.168.200.129
```

- 关闭 SELinux


## Apache (httpd)

### 概念

### 修改配置文件

- 在 `/etc/httpd/conf.d/` 下创建文件 `*.conf`
```json
<virtualhost *:80>
		servername www.aaa.com    <!-- 指定域名 -->
        documentroot /var/httpd/aaa/ <!-- 指定页面目录 -->
        <directory /var/httpd/aaa>
                require all granted
        </directory>
</virtualhost>
```

### 创建 html

- 在 `.conf` 中指定的路径下创建 `index.html`

### 启动服务