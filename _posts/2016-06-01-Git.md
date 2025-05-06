---
title: Git
categories:
- git
tags:
- git
---
### Git 全局设置
```
git config --global user.name "hqq"
git config --global user.email "hqq@hqq.com"
```
### 创建新版本库
```
git clone https://github.com/gpluscat/jetty_mybatis_api.git
cd jetty_mybatis_api
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```
### 已存在的文件夹或 Git 仓库
```
cd existing_folder
git init
git remote add origin https://github.com/gpluscat/jetty_mybatis_api.git
git add .
git commit
git push -u origin master
```