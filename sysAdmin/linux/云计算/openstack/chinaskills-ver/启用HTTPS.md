
## 规划

## 实施

### http/https

> https = http + SSL/TLS

### 配置 https

- 配置 https-repo 源
```bash
yum install -y mod_wsgi httpd mod_ssl
```

- 修改 ssl 配置文件
```git
vim /etc/httpd/conf.d/ssl.conf

- #SSLProtocol all -SSLv2 -SSLv3
+ SSLProtocol all -SSLv2 -SSLv3
+ SSLProtocol all -SSLv2
```

- 为 Dashboard 启动 SSL
```git
vim /etc/openstack-dashboard/local_settings

- #CSRF_COOKIE_SECURE = True
- #SESSION_COOKIE_SECURE = True
+ CSRF_COOKIE_SECURE = True
+ SESSION_COOKIE_SECURE = True
+ USE_SSL = True
+ SESSION_COOKIE_HTTPONLY = True


vim vim /etc/httpd/conf.d/openstack-dashboard.conf
# 从 /etc/httpd/conf.d/ssl.conf 复制
- <VirtualHost *:80>
+ <VirtualHost *:443>
+     SSLEngine
+     SSLCertificateFile /etc/pki/tls/certs/localhost.crt
+     SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
```

- 重启服务
```bash
# 重启 httpd 和 (可选)缓存
systemctl restart httpd
systemctl restart memcached
```

