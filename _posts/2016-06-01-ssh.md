---
title: ssh
categories:
- linux
tags:
- ssh
---
### ssh登录远程
```
$ ssh username@remotehost
```
输入密码

### 文件传输
```
// 上传文件
$ scp file username@remotehost:path
// 上传目录
$ scp -r dir username@remotehost:path
// 下载文件
$ scp username@remotehost:novel_api.jar ~/hqq/
// 下载目录
$ scp -r username@remotehost:logs ~/hqq/
```
上传多个文件，用空格分开
```
$ scp a.txt b.txt username@remotehost:/path/directory/
```
从远程主机复制多个文件到当前目录
```
$ scp username@remotehost:/path/directory/\{a.txt, b.txt\} .
```
