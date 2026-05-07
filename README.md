# Guacamole Docker Compose

Apache Guacamole 是一个无客户端的远程桌面网关，支持 VNC、RDP、SSH 等协议。只需浏览器即可访问远程桌面。

## 运行

```bash
# 1. 生成数据库初始化脚本
mkdir -p ./init
docker run --rm guacamole/guacamole:1.6.0 /opt/guacamole/bin/initdb.sh --postgresql > ./init/initdb.sql

# 2. 构建并启动
docker compose build
docker compose up -d
```

访问 `http://localhost:8080/guacamole/`，默认用户名 `guacadmin`，密码 `guacadmin`。

## 配置nginx反向代理

将配置放入 `/etc/nginx/sites-available/guacamole`，然后启用：

```bash
ln -sf /etc/nginx/sites-available/guacamole /etc/nginx/sites-enabled/guacamole
nginx -s reload
```

参考配置（包含SSL）：

```conf
server {
    listen 443 ssl;
    server_name _;
    ssl_certificate /opt/dev-certificates/pve.a.singapore.xphu.org.crt;
    ssl_certificate_key /opt/dev-certificates/pve.a.singapore.xphu.org.key;
    location /guacamole/ {
        proxy_pass http://127.0.0.1:8080;
        proxy_buffering off;
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $http_connection;
        access_log off;
    }
}
server {
    listen 80;
    server_name _;
    return 301 https://$host$request_uri;
}
```

## 目录说明

- `./init` - PostgreSQL 初始化脚本
- `./drive` - 共享磁盘
- `./record` - 会话录像
- `./data` - PostgreSQL 数据目录
