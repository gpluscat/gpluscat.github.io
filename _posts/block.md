---
title: block
categories: 
- iOS
tags:
- block
---
### 关于block
1.block的代码是内联的，效率高于函数调用
2.block对于外部变量默认是只读属性
3.block被Objective-C看成是对象处理
### block声明
先从一个简单的需求来说：传入两个数，并且计算这两个数的和，为此创建了这样一个block
```
int (^sum)(int a, int b) = ^(int a, int b) {
  return a + b;
}
```
名称前用^符号表示后面的字符串是block的名称,最左侧的int表示这个block的返回值，括号中间表示这个block的参数列表，这里接收两个int类型的参数。 而在等号右侧表示这个block的定义，其中返回值是可以省略的，编译器会根据上下文自动补充返回值类型。使用^符号衔接着一个参数列表，使用括号包起来，告诉编译器这是一个block，然后使用大括号将block的代码封装起来。
```
block变量声明格式
返回类型 (^block名字)(参数列表);
// 声明一个无返回值,参数为两个字符串对象,叫做aBlock的Block
void(^aBlock)(NSString *x, NSString *y);
// 形参变量名称可以省略,只留有变量类型即可
void(^aBlock)(NSString *, NSString *);
```
```
// block声明
// 返回值 block名称 参数列表
void (^blockName)(int arg1, int arg2)

// block定义
// void可省略 参数列表
void ^(int arg1, int arg2) {
  // do something
}
```
### 局部变量
block会捕获代码外的局部变量，当然这只局限于只读操作。
对于希望在block中修改的外界局部对象，我们可以给这些变量加上__block
### 循环引用
block在iOS开发中被视作是对象，因此其生命周期会一直等到持有者的生命周期结束了才会结束。另一方面，由于block捕获变量的机制，使得持有block的对象也可能被block持有，从而形成循环引用，导致两者都不能被释放

系统提供给我们__weak的关键字用来修饰对象变量，声明这是一个弱引用的对象，从而解决了循环引用的问题：
```
__weak typeof(*&self) weakSelf = self;
_cycleReferenceBlock = ^{ 
	NSLog(@"%@", weakSelf);   //弱指针引用，不会造成循环引用
};
```
### 添加block到属性及方法上
```
// 属情
@property (nonatomic, copy) NSString* (^testBlock)(int a);
// 方法
- (void)testBlock:(NSString* (^)(int a))aBlock;
```
