---
title: 防止域名被恶意解析
comments: true
categories:
  - tech
tags: [nginx, dns]
date: 2024-02-23 18:17:51
updated: 2024-02-23 18:17:51
---

# 恶意域名解析的原理

假如公网 IP 暴露，那么别人可以随意用一个域名解析到公网 IP，如果解析的域名未备案，那会有安全风险。那如何防止呢？下面是 Nignx 的解决办法：

如果 ip 对应的80（或者443）端口在 nginx 里没有指定默认资源，默认会以 conf.d 目录下的顺序第一的配置文件指向的资源为准，也就是用 80 或者 443 各排第一个的配置文件为默认资源。这时候如果用一个不相关的域名解析到 IP上，就会阴差阳错的关联到 conf.d 目录下 80 或者 443 各排第一位的配置文件对应的资源上。

防解析的解决方法也很简单，就是指定 80 和 443 端口的默认资源，让他们只返回 403 报错，默认资源没法显示有效内容，域名就没法解析成功。

# 80 端口反代配置

```nginx
server {
    listen       80 default;
    server_name  _;
    return 403;
}

```

# 443 端口反代配置

443 端口防范配置则需要配置 ssl 证书，否则所有 https 请求都会失败，下面是颁发自签名证书和配置过程。

## 生成证书

```shell
# 首先，进入你想创建证书和私钥的目录，例如：
cd /home/certs/

# 创建服务器私钥，命令会让你输入一个口令：
openssl genrsa -des3 -out server.key 2048

# 创建签名请求的证书，最后两步密码留空（CSR）：
openssl req -new -key server.key -out server.csr

# 在加载 SSL 支持的 Nginx 并使用上述私钥时除去必须的口令：
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key

# 最后标记证书使用上述私钥和 CSR：
openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt
```

## 配置 nginx

在 nginx 目录下的 conf 目录内创建 403.conf：

```nginx
# 防止域名被恶意解析
server {
    listen       80 default;
    server_name  403.abc.com;

    return 403;

    location ~/.well-known/acme-challenge/ {
        root /usr/share/nginx/html/;
    }

}

server {
    listen       443 ssl default;
    server_name  403.abc.com;
    ssl_certificate      /etc/nginx/cert/server.crt;
    ssl_certificate_key  /etc/nginx/cert/server.key;

    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    #表示使用的加密套件的类型。
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #表示使用的TLS协议的类型。
    ssl_prefer_server_ciphers on;

    return 403;

}

```

重启nginx，未报错，说明配置成功。

访问 http://ip 返回 403，说明 80 端口的防解析配置成功！

访问 https://ip 返回 403，说明 443 端口的防解析配置成功！
