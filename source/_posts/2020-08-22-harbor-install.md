---
title: docker 私服 harbor 安装
comments: true
categories:
  - 技术
tags: [harbor,docker]
date: 2020-08-22 21:32:02
updated: 2020-08-22 21:32:02
---

安装 docker-compose
```
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
验证：
docker-compose -version
PS：如果想要卸载docker-compose，请执行以下命令
sudo rm /usr/local/bin/docker-compose

https://goharbor.io/docs/2.0.0/install-config/


prepare base dir is set to /data/home/ethan/software/harbor
no config file: /data/home/ethan/software/harbor/harbor.yml
Harbor Installation Complete 

Please log out and log in or run the command 'newgrp docker' to use Docker without sudo
 
# nginx 代理配置
需要修改 harbor.yml，把https相关的注释，（如果没有注释，http会自动重定向到https,导致多次重定向）然后添加：

external_url: https://域名.com  

修改配置以后执行：
```
sudo docker-compose down
sudo ./prepare
sudo docker-compose up -d
```
nginx 配置
```
upstream harbor {
    server 192.168.1.222:10900;
}
server {
    location /harbor {
        proxy_pass http://harbor;
        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size 20m;    
        client_body_buffer_size 128k; 
        proxy_connect_timeout 90;   
        proxy_read_timeout 90;      
        proxy_buffer_size 4k;       
        proxy_buffers 6 32k;        
        proxy_busy_buffers_size 64k; 
        proxy_temp_file_write_size 64k; 
    }
}
```

docker-compose ps 命令查看启动起来的docker实例

Login to your harbor instance:
docker login -u admin https://domain.com
docker login -u admin IP
docker logout https://domain.com

### Stop Harbor
sudo docker-compose stop
### Restart harbor
sudo docker-compose start

### Reconfigure Harbor
sudo docker-compose down -v
vi harbor.yml
sudo ./prepare
如果重新配置 Harbor 安装 Notary, Clair, Chart Repository Service, 加上这些模块在命令中：
sudo ./prepare --with-notary --with-clair --with-chartmuseum
创新创建和启动 harbor 实例
sudo docker-compose up -d
