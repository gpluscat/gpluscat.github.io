---
title: CoreData
categories:
- iOS
tags:
- CoreData
---
### ios10
1.NSPersistentContainer是iOS10.0新加的一个类，它的作用是管理CoreData Stack，可以理解为是CoreData Stack的容器，使其看起来更加简洁
2.NSPersistentContainer不兼容iOS9，如果在iOS9中使用会闪退
```
#import <Foundation/Foundation.h>
#import <CoreData/CoreData.h>

@interface CoreDataManager : NSObject

@property (readonly, strong) NSPersistentContainer *persistentContainer;

+ (instancetype)sharedManager;
- (void)saveContext;

@end
```
```
#import "CoreDataManager.h"

@implementation CoreDataManager

+ (instancetype)sharedManager {
    static CoreDataManager *instance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[CoreDataManager alloc] init];
    });
    return instance;
}

#pragma mark - Core Data stack

@synthesize persistentContainer = _persistentContainer;

- (NSPersistentContainer *)persistentContainer {
    // The persistent container for the application. This implementation creates and returns a container, having loaded the store for the application to it.
    @synchronized (self) {
        if (_persistentContainer == nil) {
            _persistentContainer = [[NSPersistentContainer alloc] initWithName:@"xx"];
            [_persistentContainer loadPersistentStoresWithCompletionHandler:^(NSPersistentStoreDescription *storeDescription, NSError *error) {
                if (error != nil) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    
                    /*
                     Typical reasons for an error here include:
                     * The parent directory does not exist, cannot be created, or disallows writing.
                     * The persistent store is not accessible, due to permissions or data protection when the device is locked.
                     * The device is out of space.
                     * The store could not be migrated to the current model version.
                     Check the error message to determine what the actual problem was.
                     */
                    NSLog(@"Unresolved error %@, %@", error, error.userInfo);
                    abort();
                }
            }];
        }
    }
    
    return _persistentContainer;
}

#pragma mark - Core Data Saving support

- (void)saveContext {
    NSManagedObjectContext *context = self.persistentContainer.viewContext;
    NSError *error = nil;
    if ([context hasChanges] && ![context save:&error]) {
        // Replace this implementation with code to handle the error appropriately.
        // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
        NSLog(@"Unresolved error %@, %@", error, error.userInfo);
        abort();
    }
}

@end
```

### ios9
```
#import <Foundation/Foundation.h>
#import <CoreData/CoreData.h>

@interface CoreDataManager : NSObject

+ (instancetype)sharedManager;

@property (nonatomic, readonly) NSPersistentStoreCoordinator *persistentStoreCoordinator;
@property (nonatomic, readonly) NSManagedObjectContext *managedObjectContext;

@end
```
```
//
//  CoreDataManager.m
//
//  Created by qing on 2017/3/22.
//  Copyright © 2017年 . All rights reserved.
//

#import "CoreDataManager.h"

@interface CoreDataManager()

@property (nonatomic, strong) NSManagedObjectModel *managedObjectModel;
@property (nonatomic, strong) NSPersistentStoreCoordinator *persistentStoreCoordinator;
@property (nonatomic, strong) NSManagedObjectContext *managedObjectContext;

@end

@implementation CoreDataManager

+ (instancetype)sharedManager {
    
    static CoreDataManager *sharedManager = nil;
    static dispatch_once_t once;
    dispatch_once(&once, ^{
        sharedManager = [[self alloc] init];
    });
    
    return sharedManager;
}

// Returns the path to the application's documents directory.
- (NSString *)applicationCachesDirectory {
    return [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
}

- (NSManagedObjectModel *)managedObjectModel {
    
    if (_managedObjectModel) {
        return _managedObjectModel;
    }
    
    NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"xx" withExtension:@"momd"];
    _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
    
    return _managedObjectModel;
}

// Returns the persistent store coordinator for the application.
// If the coordinator doesn't already exist, it is created and the application's store added to it
//
- (NSPersistentStoreCoordinator *)persistentStoreCoordinator {
    
    if (_persistentStoreCoordinator != nil) {
        return _persistentStoreCoordinator;
    }
    
    // find the earthquake data in our Documents folder
    NSString *storePath = [[self applicationCachesDirectory] stringByAppendingPathComponent:@"bookshelf.sqlite"];
    NSURL *storeUrl = [NSURL fileURLWithPath:storePath];
    
    NSError *error;
    _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:self.managedObjectModel];
    if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeUrl options:nil error:&error]) {
        // Replace this implementation with code to handle the error appropriately.
        // abort() causes the application to generate a crash log and terminate.
        // You should not use this function in a shipping application, although it may be useful
        // during development. If it is not possible to recover from the error, display an alert
        // panel that instructs the user to quit the application by pressing the Home button.
        //
        
        // Typical reasons for an error here include:
        // The persistent store is not accessible
        // The schema for the persistent store is incompatible with current managed object model
        // Check the error message to determine what the actual problem was.
        //
//        [[NSFileManager defaultManager] removeItemAtURL:storeUrl error:nil]
        NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
        abort();
    }
    
    return _persistentStoreCoordinator;
}

// The managed object context for the view controller (which is bound to the persistent store coordinator for the application).
- (NSManagedObjectContext *)managedObjectContext {
    
    if (_managedObjectContext) {
        return _managedObjectContext;
    }
    
    _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSMainQueueConcurrencyType];
    _managedObjectContext.persistentStoreCoordinator = self.persistentStoreCoordinator;
    
    // observe the APLEarthQuakeSource save operation with its managed object context
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(mergeChanges:)
                                                 name:NSManagedObjectContextDidSaveNotification
                                               object:nil];
    
    return _managedObjectContext;
}

// merge changes to main context,fetchedRequestController will automatically monitor the changes and update tableview.
- (void)updateMainContext:(NSNotification *)notification {
    
    assert([NSThread isMainThread]);
    [self.managedObjectContext mergeChangesFromContextDidSaveNotification:notification];
}

// this is called via observing "NSManagedObjectContextDidSaveNotification" from our APLEarthQuakeSource
- (void)mergeChanges:(NSNotification *)notification {
    
    if (notification.object != self.managedObjectContext) {
        [self performSelectorOnMainThread:@selector(updateMainContext:) withObject:notification waitUntilDone:NO];
    }
}

@end

```
在xcdatamodeld创建相应的model
可以选择objc或swift
引用
```
#import "Model+CoreDataClass.h"
```
### 对CoreData进行增删改查时的区别
iOS9和iOS10在对CoreData进行增删改查操作没有区别
1.增加数据
```
    CoreDataManager *manager = [CoreDataManager sharedManager];
    NSManagedObjectContext *context = manager.persistentContainer.viewContext;

    Person *person = [NSEntityDescription insertNewObjectForEntityForName:NSStringFromClass([Person class]) inManagedObjectContext:context];
    person.name = @"小三";
    person.age = 32;
    
    if([context save:nil]) {
        NSLog(@"添加成功");
    }
```
2.查询及修改数据
```
    NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"Person"];
    //2.创建查询谓词（查询条件）
    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"name == %@",@"小三"];
    //3.给查询请求设置谓词
    request.predicate = predicate;
    //4.查询数据
    NSArray<Person*> *arr = [context executeFetchRequest:request error:nil];
    arr.firstObject.age = 56;
    if([context save:nil]) {
        NSLog(@"修改成功");
    }
```
3.删除数据
```
    NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"Person"];
    //2.创建查询谓词（查询条件）
    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"name == %@",@"小三"];
    //3.给查询请求设置谓词
    request.predicate = predicate;
    //4.查询数据
    NSArray<Person*> *arr = [context executeFetchRequest:request error:nil];
    [context deleteObject:arr.firstObject];
    if([context save:nil]) {
        NSLog(@"删除成功");
    }
```
4.NSFetchRequest
排序
```
    NSSortDescriptor *sort = [NSSortDescriptor sortDescriptorWithKey:@"createDate" ascending:NO];
    //设置查找请求的排序描述器
    [request setSortDescriptors:[NSArray arrayWithObject:sort]];
```
查找条件
```
    request.predicate =  [NSPredicate predicateWithFormat:@"novelId = %@ AND chapterId = %@", _novelId, _chapterId];
```
### 存储文件路径区别
1.iOS9之前，是存储在Documents中
2.iOS10.0，数据库文件存储在Library->Application Support中