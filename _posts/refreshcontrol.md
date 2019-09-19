---
title:  给UIScrollView添加属性
date:   2015-05-22 17:07:00
categories: 
- iOS
---

### UIScrollView
```
@interface UIScrollView(UIRefreshControl)
@property(nonatomic, strong, readonly) UIRefreshControl *refreshControl;
- (void)addPullToRefresh:(void (^)(void))block;
- (void)beginRefreshing;
- (void)endRefreshing;
@end
```
### import <objc/runtime.h>
```
@interface PullToRefreshView : UIRefreshControl
@property(nonatomic, copy) void (^pullToRefreshBlock)(void);
@end

@implementation PullToRefreshView

- (instancetype)init
{
    self = [super init];
    if(self)
    {
        [self addTarget:self action:@selector(pulledToRefresh:) forControlEvents:UIControlEventValueChanged];
    }
    return self;
}

- (void)pulledToRefresh:(UIRefreshControl *)refreshControl
{
    if(self.pullToRefreshBlock)
        self.pullToRefreshBlock();
}

@end

static char UIScrollViewRefreshControl;
@implementation UIScrollView(UIRefreshControl)
@dynamic refreshControl;

- (void)addPullToRefresh:(void (^)(void))block
{
    if(!self.refreshControl)
    {
        PullToRefreshView *refresh = [[PullToRefreshView alloc] init];
        refresh.tintColor = [UIColor redColor];
        refresh.pullToRefreshBlock = block;
        [self addSubview:refresh];

        self.refreshControl = refresh;
    }
}

- (UIRefreshControl *)refreshControl
{
    return objc_getAssociatedObject(self, &UIScrollViewRefreshControl);
}

- (void)setRefreshControl:(UIRefreshControl *)refreshControl
{
    objc_setAssociatedObject(self, &UIScrollViewRefreshControl, refreshControl, OBJC_ASSOCIATION_ASSIGN);
}

- (void)beginRefreshing
{
    [self.refreshControl beginRefreshing];
}

- (void)endRefreshing
{
    if(self.refreshControl.isRefreshing)
    {
        __weak UIScrollView *weakSelf = self;
        double delayInSeconds = 0.01f;
        dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayInSeconds * NSEC_PER_SEC));
        dispatch_after(popTime, dispatch_get_main_queue(), ^(void){
            [weakSelf.refreshControl endRefreshing];
        });
    }
}

@end
```