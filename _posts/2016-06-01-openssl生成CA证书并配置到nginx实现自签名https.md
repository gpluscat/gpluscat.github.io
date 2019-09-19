---
title:  openssl生成CA证书并配置到nginx实现自签名https
categories: 
- iOS
tags:
- https
- ca
- openssl
- nginx
---
```
$ mkdir ca
$ cd ca
```
### 生成证书
1.为根生成一个2048位的SHA-256 RSA私钥
```
$ openssl genrsa -aes256 -out a.key 2048
```
2.拷贝一个不需要输入密码的密钥文件
```
$ openssl rsa -in a.key -out nopass.key
```
3.生成一个证书请求
```
$ openssl req -new -key a.key -out a.csr
```
4.自己签发证书
```
$ openssl req -sha256 -new -x509 -days 3650 -key a.key -out a.crt
```
输入时要注意
Country 是2位的简码
Common Name 是你的域名或ip

### 配置证书到nginx
vi编辑nginx根目录下的nginx.conf文件
```
# HTTPS server
    server {
        listen       443 ssl;
        server_name  localhost;
        ssl          on;
        ssl_certificate      a.crt;
        ssl_certificate_key  nopass.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        location / {
            proxy_pass http://localhost:8080/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
```
nginx listen 默认端口是80，443端口时可以在最终的https地址上忽略不写的
nginx 服务器重启，关闭命令
nginx -s reload  ：修改配置后重新加载生效
nginx -s reopen  ：重新打开日志文件
nginx -t -c /path/to/nginx.conf 测试nginx配置文件是否正确
关闭nginx：
nginx -s stop  :快速停止nginx

其他的停止nginx 方式：
ps -ef | grep nginx
kill -QUIT 主进程号     ：从容停止Nginx
kill -TERM 主进程号     ：快速停止Nginx
pkill -9 nginx          ：强制停止Nginx