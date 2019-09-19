---
title:  UISearchDisplayController
date:   2015-05-05 11:00:00
categories: 
- iOS
tags:
- uisearchdisplaycontroller
---

### UISearchDisplayController全局变量

### 实现以下方法
```
- (BOOL)searchDisplayController:(UISearchDisplayController *)controller shouldReloadTableForSearchString:(NSString *)searchString

- (BOOL)searchDisplayController:(UISearchDisplayController *)controller shouldReloadTableForSearchScope:(NSInteger)searchOption

```
### 对象及数组操作
```
NSPredicate *resultPredicate = [NSPredicate predicateWithFormat:@"SELF.userNickname contains[cd] %@ OR SELF.userRemake contains[cd] %@",                                     searchText, searchText];
NSArray *filterContracts = [allContracts filteredArrayUsingPredicate:resultPredicate];
```
如果是单个字符串或dict,利用
```
NSPredicate *predText = [NSPredicate predicateWithFormat:@"SELF contains[cd] %@",self.searchText];
if([predText evaluateWithObject:nameStr])
{
	// 处理
}
```