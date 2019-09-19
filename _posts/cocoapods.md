---
title:  使用CocoaPods管理依赖库
date:   2015-04-30 10:00:00
categories: 
- iOS
tags:
- CocoaPods
---
### 设置gem sources
```
$ gem sources
$ gem sources -r https://rubygems.org/ 
$ gem sources -a https://ruby.taobao.org/
```
### 查看是否使用的是淘宝镜像
```
$ gem sources -l
```
### 安装
```
$ sudo gem install cocoapods
```
### 安装成功后，查看版本
```
$ pod --version
```
### 接下来进行安装，扫行
```
$ pod setup
```
估计要等一段时间...
### pod相关命令
```
# 搜索
$ pod search AFNetworking
$ pod install
$ pod update
```
切到我们的工程根目录下
```
$ vim Podfile
# 添加
target 'oc2js' do 
  pod 'WebViewJavascriptBridge', '~> 6.0'
end
# wq保存退出
$ pod install
```
### 注意
如果import时没有发现文件，可以在target-Build Settings下修改“User Header Search Paths”项，新增${SRCROOT}并选择rcursive

如果是直接使用已有CocoaPods的项目，则需要首先运行一下pod update命令来更新项，然后照样通过.xcworkspace来打开工程

如果需要依赖多个第三方类库，只需要修改Podfile文件的配置，然后运行pod update命令即可，比如新增一个AFNetworking的依赖库，首先执行pod search AFNetworking查看一下AFNetworking的配置信息，修改Podfile文件，在后面增加AFNetworking的对应配置信息，然后运行pod update命令就完成了对AFNetworking的集成。

如果类库有更新，查看更新配置并执行pod update即可简单完成了，从此从手动更新繁重的体力劳动中解脱出来。

安装CocoaPods
新建项目并在工程根目录下新建Podfile文件，配置需要管理的第三方库
运行pod install下载安装第三方库