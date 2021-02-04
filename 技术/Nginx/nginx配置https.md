# nginx 配置 https

```
server {
    listen 443 ssl;
    server_name xxx.com www.xxx.com;

    # 指定 ssl 证书路径
    ssl_certificate /etc/ssl/certs/3568491_xxx.com.pem;
    # 指定私钥文件路径
    ssl_certificate_key /etc/ssl/certs/3568491_xxx.com.key;
}
```
