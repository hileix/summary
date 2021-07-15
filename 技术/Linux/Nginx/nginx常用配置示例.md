# nginx 常用配置示例

nginx 配置文件在 `/etc/nginx` 目录下。

`/etc/nginx/nginx.conf` 是 nginx 的配置文件，在这个配置文件中，会有：

```nginx
http{
  # 在 /etc/nginx/conf.d/ 文件夹下所有以 .conf 结尾的配置文件也有效
  include /etc/nginx/conf.d/*.conf;
}
```

所以我们一般会自定义的配置放在 `/etc/nginx/conf.d/` 文件夹中。

自定义的 nginx 配置文件命名需要遵循这样的规则：`项目名称.端口号.conf`。如：shop.8888.conf

## 1. 单页应用配置示例

```nginx
server {
  # 监听的端口号。外部可以通过该端口号访问到本 server
  listen 8888;
  # 服务器名称，也就是别人访问你服务的 ip 地址或域名，可以写多个，用空格隔开
  server_name 118.178.123.78 test.realsun.me;

  location / {
    # 前端静态资源的目录
    alias /var/www/shop/;
    index index.html;
    try_files $uri $uri/ /index.html;
  }
}
```

## 2. 后端应用配置示例

如 nodejs 应用部署在服务器。

```nginx
server {
  listen 7001;
  server_name 118.178.123.78 test.realsun.me;

  location ~ /api/(.*)$ {
    # 请求将会转发到 7701 端口
    proxy_pass         http://localhost:7701;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-Nginx-Proxy true;
    proxy_set_header Connection "";
  }
}
```
