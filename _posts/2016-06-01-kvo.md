---
title:  KVO
date:   2015-04-28 11:00:00
categories: 
- iOS
tags:
- kvo
- kvc
---

KVO,即：Key-Value Observing，它提供一种机制，当指定的对象的属性被修改后，则对象就会接受到通知。简单的说就是每次指定的被观察的对象的属性被修改后，KVO就会自动通知相应的观察者了。

Key-Value Coding (KVC)

KVC，即是指 NSKeyValueCoding，一个非正式的 Protocol，提供一种机制来间接访问对象的属性。KVO 就是基于 KVC 实现的关键技术之一。

一个对象拥有某些属性。比如说，一个 Person 对象有一个 name 和一个 address 属性。以 KVC 说法，Person 对象分别有一个 value 对应他的 name 和 address 的 值。 key 只是一个字符串，它对应的值可以是任意类型的对象。从最基础的层次上看，KVC 有两个方法：一个是设置 key 的值，另一个是获取 key 的

```
void changeName(Person *p, NSString *newName)
{
    // using the KVC accessor (getter) method
    NSString *originalName = [p valueForKey:@"name"];
    // using the KVC  accessor (setter) method.
    [p setValue:newName forKey:@"name"];
     
    NSLog(@"Changed %@'s name to: %@", originalName, newName);
}
#=> 如果 Person 有另外一个 key 配偶（spouse），spouse 的 key 值是另一个 Person 对象，用 KVC 可以这样写
void logMarriage(Person *p)
{
    // just using the accessor again, same as example above
    NSString *personsName = [p valueForKey:@"name"];
     
    // this line is different, because it is using
    // a "key path" instead of a normal "key"
    NSString *spousesName = [p valueForKeyPath:@"spouse.name"];
     
    NSLog(@"%@ is happily married to %@", personsName, spousesName);

}
```

key 与 key path 要区分开来，key 可以从一个对象中获取值，而 key path 可以将多个 key 用点号 “.” 分割连接起来，比如：
```
[p valueForKeyPath:@"spouse.name"];
```

相当于这样……
```
[[p valueForKey:@"spouse"] valueForKey:@"name"];

(void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(void *)context;

(void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath context:(void *)context;

(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context;
```