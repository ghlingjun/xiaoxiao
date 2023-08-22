---
title: docker 私服 harbor 安装
comments: true
categories:
  - 技术
tags: [harbor,docker]
date: 2020-08-22 21:32:02
updated: 2020-08-22 21:32:02
---

# 1 安装 docker-compose

```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
验证：
docker-compose -version
PS：如果想要卸载docker-compose，请执行以下命令
`sudo rm /usr/local/bin/docker-compose`

# 2 安装 harbor

本文是参照官方文档进行编写的。https://goharbor.io/docs/2.0.0/install-config/

## 2.1 下载安装包

如果服务器可以联网直接下载在线安装包(`harbor-offline-installer-v2.8.2.tgz`)。

然后解压安装包：

```sh
tar xzvf harbor-online-installer-version.tgz
```

## 2.2 配置 Https Access

### 生成授权证书

生成 CA 证书的秘钥：

```sh
openssl genrsa -out ca.key 4096
```

生成 CA 证书：

`-subj`参数的值反映了自己的机构信息。

```sh
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=shumei/OU=Personal/CN=ahshumei.com" \
 -key ca.key \
 -out ca.crt
```

### 生成服务端证书

1. 生成秘钥：

```sh
openssl genrsa -out ahshumei.com.key 4096
```

2. 生成 a certificate signing request (CSR)

```sh
openssl req -sha512 -new \
    -subj "/C=CN/ST=Beijing/L=Beijing/O=shumei/OU=Personal/CN=ahshumei.com" \
    -key ahshumei.com.key \
    -out ahshumei.com.csr
```

3. Generate an x509 v3 extension file

```sh
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=ahshumei.com
DNS.2=ahshumei
DNS.3=服务器外网 IP
EOF
```

4. 使用 `v3.ext` 为 harbor 服务生成一个证书

```sh
openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in ahshumei.com.csr \
    -out ahshumei.com.crt
```

### 配置证书到 Harbor 和 Docker

生成 ca.acrt、ahshumei.com.crt、和 ahshumei.com.key 文件后，我们要将他们配置到 harbor 和 docker。

1. 复制服务端证书和 key 到harbor 服务器的证书文件夹

```sh
cp ahshumei.com.crt /home/shumei/soft/harbor/cert
cp ahshumei.com.key /home/shumei/soft/harbor/cert
```

2. 将 `yourdomain.com.crt` 转换为 `yourdomain.com.cert`， Docker 会用到。

```sh
openssl x509 -inform PEM -in ahshumei.com.crt -out ahshumei.com.cert
```

3. 复制服务器证书、key、和 CA 文件到harbor 服务器的 Docker 证书目录。

```sh
cp ahshumei.com.cert /etc/docker/certs.d/ahshumei.com:11005/
cp ahshumei.com.key /etc/docker/certs.d/ahshumei.com:11005/
cp ca.crt /etc/docker/certs.d/ahshumei.com:11005/
```

如果将 nginx 的 443 端口映射到了其他端口，证书文件夹目录创建为：`/etc/docker/certs.d/ahshumei.com:prot/`或者`/etc/docker/certs.d/harbor_ip:prot`，否则不需要加端口。

4. 重启 docker 引擎

```sh
systemctl restart docker
# 或
sudo service docker restart
```

证书目录结构如下：

```fallback
/etc/docker/certs.d/
    └── ahshumei.com:11005
       ├── ahshumei.com.cert  <-- Server certificate signed by CA
       ├── ahshumei.com.key   <-- Server key signed by CA
       └── ca.crt        <-- Certificate authority that signed the registry certificate
```

## 2.3 部署 harbor

### 配置必须的参数

```yaml
hostname：ahshuemi.com
https: 
		port: 11005
		certificate: /home/shumei/soft/harbor/cert/ahshumei.com.cert
		private_key: /home/shumei/soft/harbor/cert/ahshumei.com.key
harbor_admin_password： 修改
database: 
		password: 修改
data_volume: /home/shumei/soft/harbor/data
```

### 执行安装脚本

```sh
sudo ./install.sh
```

安装后默认会启动。

如果 `harbor` 已经安装过，修改 `harbor.yml` 后需要先执行 `prepare` 脚本：

```sh
./prepare
```

如果 harbor 服务启动着，需要先关闭服务并移除存在的实例（镜像数据不会丢失）：

```sh
docker-compose down -v
```

启动 harbor

```sh
docker-compose up -d
```

### 验证是否启动成功

1. 浏览器访问 `https://ahshumei.com:11005`，应该可以打开 harder 服务地址。
2. 查看`/etc/docker/daemon.json`确保`-insecure-registry`配置项中不包含`https://ahshumei.com:11005`。
3. 使用 Docker client 登录 harbor。

```sh
docker login ahshumei.com:11005
```

# 相关命令

### Stop Harbor

sudo docker-compose stop
### Restart harbor

sudo docker-compose start

# 参考文档
Harbor Installation and Configuration[https://goharbor.io/docs/2.0.0/install-config/]
