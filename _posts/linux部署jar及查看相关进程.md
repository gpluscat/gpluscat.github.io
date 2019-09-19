---
title: linux部署jar及查看相关进程
categories:
- linux
tags:
- linux
- jar
---
### 后端运行jar
```
$ nohup java -jar api.jar 2>&1 &
```
注意：所依赖的lib也要上传
### 查看进程
```
$ ps -ef|grep java
```
### 杀死进程
```
$ kill -9 pid
```
### 查看日志
```
$ tail -n 1000 payment.log
```
