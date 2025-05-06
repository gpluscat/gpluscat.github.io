### 查看问题

```
pod setup --verbose
```
下载到本地，不过我们不要忘记它是个git管理的东西，我们还得把我们自己创建的那个目录git化：
```
# 解压zip，放入~/.cocoapods/repos/，改名为master
git init
git remote add origin https://github.com/CocoaPods/Specs.git
git remote -v #和git clone没啥区别了
```