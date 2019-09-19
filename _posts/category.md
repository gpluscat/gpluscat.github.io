---
title: 类别
date: 2015-04-27 16:00:00
categories: 
- iOS
tags:
- Category
- runtime
---
给现有类添加属性

1 <objc/runtime.h>

2 @dynamic progressView

3 
objc_getAssociatedObject
objc_setAssociatedObject

```
- (UIView *)progressView
  {
    return objc_getAssociatedObject(self, &UINavigationBarProgress);
  }
- (void)setProgressView:(UIView *)progressView
  {
    objc_setAssociatedObject(self, &UINavigationBarProgress, progressView, OBJC_ASSOCIATION_ASSIGN);
  }
```
