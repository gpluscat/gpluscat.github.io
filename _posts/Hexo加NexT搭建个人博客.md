---
title: Hexo加NexT搭建个人博客
categories: 
- Hexo
tags:
- Hexo
---
### 首先安装Node.js和Git
安装好之后，可使用npm安装Hexo
```
$ npm install -g hexo-cli
```
然后执行下列命令
```
$ hexo init blog
$ cd blog
$ npm install
```
### 安装NexT
在终端窗口下，定位到站点目录下
```
$ cd blog
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
站点配置文件，启用主题
```
theme: next
```
### 服务器安装nginx
```
server {
        listen       8080 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
            root /usr/local/www/blog;
            try_files $uri $uri/ /index.html;
        }
}
```
最后是上传html到服务器下，修改站点配制文件
```
deploy:
  type: sftp
  host: xxx
  user: xxx
  pass: xxx
  remotePath: /usr/local/www/blog/
```
xxx填写你服务器的主机ip，登录名和密码，上传
```
$ hexo clean
$ hexo d -g
```
大功告成...

### 相关资料
[Hexo](https://hexo.io/)
[NexT](http://theme-next.iissnan.com/)

