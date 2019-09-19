---
title:  util类
date:   2015-04-27 15:47:26
categories: 
- iOS
---
### 获取指定color的图片
```
UIImage* getImageFromRectColor(CGRect rect, UIColor *color)
{
    UIGraphicsBeginImageContext(rect.size);
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextSetFillColorWithColor(context, [color CGColor]);
    CGContextFillRect(context, rect);
    UIImage *img = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();
    return img;
}
```
### 根据系统分辨率获取资源图
```
NSString* getResPath(NSString *name)
{
    int scale = [UIScreen mainScreen].scale;
    NSString *fileName = [NSString stringWithFormat:@"%@@%dx", name, scale];
    NSString *path = [[NSBundle mainBundle] pathForResource:fileName ofType:@"png"];
    return path;
}
```
