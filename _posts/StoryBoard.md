---
title: StoryBoard
categories:
- iOS
tags:
- storyboard
---
Storyboard是苹果官方主推的一个代替xib的策略。
![storyboard](https://github.com/gpluscat/_posts/blob/master/images/storyboard.png?raw=true)
### storyboard优势
storyboard能代替nib自然有其优势，一般来说storyboard具有以下几种优点：
1.storyboard能将nib汇总统一管理
2.storyboard可以描述各种场景之间的过渡，这种过渡被称作segue，storyboard 把 view controller叫做：scene，可以通过拖拽实现过度，减少代码
3.支持tableview的prototype cell，可以在storyboard中编辑cell，减少代码量
### 启动storyboard
加载storyboard肯定是需要一个主入口的，这个主入口在info.plist中
### 创建relationship segue
对于三大container view controller，即Tab Bar Controller，Navigation Controller，Split View Controller ，可以通过拖拽创建设置relationship segue。
### 命名tabbar controller的tabbar
并非在tabbar controller里，而是在与其相连、对应的controller里改动
navigation bar 的 title 也是同理。但是，强烈不建议在storyboard里设置navigationbar，因为storyboard是为了简化操作的，但是设置navigationbar太麻烦了，还不如代码方便实用。
### 设置ViewController对应的类
选中相应ViewController，然后在 Custom Class 内写上相应类名即可
### 使用segue
1.什么是Segue
Storyboard上每一根用来界面跳转的线，都是一个UIStoryboardSegue对象（简称Segue）
2.Segue的属性
每一个Segue对象，都有3个属性
```
// 唯一标识
@property (nonatomic, readonly) NSString *identifier;
// 来源控制器
@property (nonatomic, readonly) id sourceViewController;
// 目标控制器
@property (nonatomic, readonly) id destinationViewController;
```
3.Segue的类型
根据Segue的执行（跳转）时刻，Segue可以分为2大类型:
* 自动型(Action segue)：点击某个控件后（比如按钮），自动执行Segue，自动完成界面跳转。
* 手动型(Manual segue)：需要通过写代码手动执行Segue，才能完成界面跳转。
  4.segue执行过程
  手动调用performSegueWithIdentifier:sender:方法实现跳转。那么这期间发生了什么呢？大致分为三个部分。
* 根据identifier去storyboard中找到对应的线，新建UIStoryboardSegue对象
```
- (instancetype)initWithIdentifier:(NSString *)identifier source:(UIViewController *)source destination:(UIViewController *)destination; // Designated initializer
```
就是执行了UIStoryboardSegue中initWithIdentifier:source:destination:方法,并且identifier就是在Storyboard中Segue属性设置的标识. 来源就是连线的头部. 目标就是连线尾
* 调用sourceViewController的下面方法，做一些跳转前的准备工作并且传入创建好的Segue对象
```
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender;
```
所谓跳转前的准备，因为可以拿到Segue(来源控制器,目标控制器)，所以就可以在这里给下一个控制器传递数据。这个方法是系统默认调用，所以只需要实现即可。另外，只能由来源控制器调用,来拿到目标控制器。
* 调用Segue对象的perform方法开始执行界面跳转操作。


### 跳进

Action segue 比较简单，就是将button连到要展示的viewController上，当点击时，就会触发。
Manual segue 相对比较麻烦，但是比较灵活。它设置了两个viewController的跳转关系，在你需要的时候出发跳转。
```
//根据 segue Identifier跳转界面
[self performSegueWithIdentifier:@"Identifier" sender:self];
```
使用纯代码
```
	UIViewController *controller = [storyboard instantiateViewControllerWithIdentifier:@"controller"];
    //显示ViewController
    [self presentViewController:controller animated:YES completion:nil];
```
### 跳出
A->B现在要从B跳回A
要在A界面加入,返回类型一定是IBAction，参数类型一定是UIStoryboardSegue，名称随便
```
//其他界面返回到此界面调用的方法
- (IBAction)viewControllerUnwindSegue:(UIStoryboardSegue *)unwindSegue {
}
```
右键B界面上方的Exit，弹出菜单选择manual，这里连接自己表示要在当前viewcontroller中用代码的方式回退。
现在就可以在界面B中的任意时候调用方法回退了
```
- (IBAction)back:(id)sender {
　　//执行segue跳页的方法
    [self performSegueWithIdentifier:@"fromBToA" sender:nil];
}
```
### storyboard reference
iOS9中，苹果引入了 storyboard reference 用以减小storyboard的体积，方便管理（并不知道iOS9之前怎么用多个storyboard）
选中想要拆分的viewcontroller，然后在菜单栏干中“Editor->Refactor to Storyboard”，如下图所示。然后命名新的storyboard即可