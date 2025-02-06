### Nginx 使用

Nginx 是一个高性能的 HTTP 服务器和反向代理服务器，广泛用于负载均衡、静态资源服务和反向代理等场景。以下是 Nginx 的核心使用方法和配置。

------

### Nginx 文件结构

Nginx 的典型文件结构如下（基于 Linux 系统）：

- **配置文件目录**:
  - `/etc/nginx/`: 主配置目录。
    - `nginx.conf`: 主配置文件。
    - `sites-available/`: 可用的虚拟主机配置。
    - `sites-enabled/`: 启用的虚拟主机配置（通常是 `sites-available/` 的符号链接）。
    - `conf.d/`: 额外的配置文件目录。
  - `/usr/local/nginx/conf/`: 如果从源码编译安装，配置文件可能在此目录。
- **日志文件目录**:
  - `/var/log/nginx/`: 日志文件目录。
    - `access.log`: 访问日志。
    - `error.log`: 错误日志。
- **默认网站根目录**:
  - `/usr/share/nginx/html/` 或 `/var/www/html/`: 默认的静态文件存放目录。
- **可执行文件**:
  - `/usr/sbin/nginx` 或 `/usr/local/nginx/sbin/nginx`: Nginx 可执行文件。

------

### Nginx 配置文件及编写规范

#### 1. 配置文件结构

Nginx 配置文件采用层级结构，主要分为以下几个部分：

- **全局块**: 配置全局生效的参数，如用户、工作进程数等。
- **events 块**: 配置网络连接相关的参数。
- **http 块**: 配置 HTTP 相关的参数，可以包含多个 `server` 块。
- **server 块**: 定义虚拟主机，每个 `server` 块对应一个域名或 IP。
- **location 块**: 定义请求的匹配规则和处理方式。

#### 2. 示例配置文件

```
# 全局块
user www-data;
worker_processes auto;
error_log /var/log/nginx/error.log;

# events 块
events {
    worker_connections 1024;
}

# http 块
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # 定义日志格式
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    # 定义一个 server 块
    server {
        listen 80;
        server_name example.com;

        # 定义一个 location 块
        location / {
            root /var/www/html;
            index index.html;
        }

        # 反向代理配置
        location /api/ {
            proxy_pass http://backend_server;
        }
    }
}
```

#### 3. 编写规范

- 使用 `#` 添加注释。
- 每个指令以分号 `;` 结尾。
- 使用 `{}` 包裹块级配置。
- 使用 `include` 引入其他配置文件。
- 配置文件修改后，使用 `nginx -t` 测试语法是否正确。

------

### Nginx 反向代理配置

反向代理是 Nginx 的核心功能之一，用于将客户端请求转发到后端服务器。

#### 1. 基本反向代理配置

```
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

- `proxy_pass`: 指定后端服务器的地址。
- `proxy_set_header`: 设置请求头，确保后端服务器能获取真实的客户端信息。

#### 2. 负载均衡配置

Nginx 可以作为负载均衡器，将请求分发到多个后端服务器。

```
http {
    upstream backend {
        server 192.168.1.101;
        server 192.168.1.102;
        server 192.168.1.103;
    }

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend;
        }
    }
}
```

- `upstream`: 定义后端服务器组。
- `server`: 定义后端服务器的地址。

------

### Nginx 常用命令

#### 1. 启动 Nginx

```
sudo systemctl start nginx  # Linux
sudo brew services start nginx  # macOS
start nginx  # Windows
```

#### 2. 停止 Nginx

```
sudo systemctl stop nginx  # Linux
sudo brew services stop nginx  # macOS
nginx -s stop  # Windows
```

#### 3. 重启 Nginx

```
sudo systemctl restart nginx  # Linux
sudo brew services restart nginx  # macOS
nginx -s reload  # Windows
```

#### 4. 重载配置文件

```
sudo systemctl reload nginx  # Linux
sudo brew services reload nginx  # macOS
nginx -s reload  # Windows
```

#### 5. 测试配置文件

```
sudo nginx -t
```

#### 6. 查看 Nginx 版本

```
nginx -v  # 查看版本
nginx -V  # 查看版本和编译参数
```

#### 7. 查看运行状态

```
sudo systemctl status nginx  # Linux
sudo brew services list  # macOS
```