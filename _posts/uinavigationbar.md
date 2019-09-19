---
title:  UINavigationBar设置标题的颜色
date:   2015-04-29 17:50:00
categories: 
- iOS
tags:
- UINavigationBar
---

放到AppDelegate中
```
UINavigationBar *bar = [UINavigationBar appearance];
[bar setTitleTextAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:30], NSForegroundColorAttributeName:[UIColor redColor]}];
```