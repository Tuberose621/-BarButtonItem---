**Encapsulates a TabBarItem--封装一个BarButtonItem类**
- 在我们程序的导航栏的左边或右边一般都会有这样的BarButtonItem，用来界面之间的跳转
- 如果我们有很多的控制器，那么我们就会有很多的BarButtonItem要写
- 所以我们要对它进行封装，今后可以更方便的使用它，拿起来就可以用


![](http://upload-images.jianshu.io/upload_images/739863-5f0012d9fa113e5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 首先，有几个小技术点：

  - 三种方式实现按钮尺寸和要设置的图片尺寸一样

```objc
第一种方法：
UIImage *image = [UIImage imageNamed:@"tupian"];
UIImage *image =[button backgroundImageForState:UIControlStateNormal];
button.frame = CGRectMake(0, 0, image.size.width, image.size.height);

第二种方法：
UIImage *image = [UIImage imageNamed:@"tupian"];
UIImage *image = button.currentbackgrondImage；
button.frame = CGRectMake(0, 0, image.size.width, image.size.height);

第三种方法：
UIImage *image = [UIImage imageNamed:@"tupian"];
UIImage *image =[button backgroundImageForState:UIControlStateNormal];
[button sizeToFit];
```

- 每一个导航控制器都可以设置对应的按钮

```objc
UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
[button setBackgroundImage:[UIImage imageNamed:@"MainTagSubIcon"] forState:UIControlStateNormal];
[button setBackgroundImage:[UIImage imageNamed:@"MainTagSubIconClick"] forState:UIControlStateHighlighted];
[button sizeToFit];
[button addTarget:self action:@selector(CYEssenceClick) forControlEvents:UIControlEventTouchUpInside];
self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc] initWithCustomView:button];

- (void)CYEssenceClick
{
    NSLog(@"%s",__func__);
}
```
- 如果设置的按钮一边有两个，就放在数组中，数组中的顺序和它显示的顺序有关

![](http://upload-images.jianshu.io/upload_images/739863-d790ee9d2ead3636.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```objc
        UIButton *moonButton = [UIButton buttonWithType:UIButtonTypeCustom];
        [moonButton setBackgroundImage:[UIImage imageNamed:@"mine-moon-icon"] forState:UIControlStateNormal];
        [moonButton setBackgroundImage:[UIImage imageNamed:@"mine-moon-icon-click"] forState:UIControlStateHighlighted];
        [moonButton sizeToFit];
        [moonButton addTarget:self action:@selector(moonClick) forControlEvents:UIControlEventTouchUpInside];

        UIButton *settingButton = [UIButton buttonWithType:UIButtonTypeCustom];
        [settingButton setBackgroundImage:[UIImage imageNamed:@"mine-setting-icon"] forState:UIControlStateNormal];
        [settingButton setBackgroundImage:[UIImage imageNamed:@"mine-setting-icon-click"] forState:UIControlStateHighlighted];
        [settingButton sizeToFit];
        [settingButton addTarget:self action:@selector(settingClick) forControlEvents:UIControlEventTouchUpInside];

            self.navigationItem.rightBarButtonItems = @[
                                                   [[UIBarButtonItem alloc] initWithCustomView:settingButton],
                                                   [[UIBarButtonItem alloc] initWithCustomView:moonButton]
];

- (void)moonClick
{
    NSLog(@"%s",__func__);
}
- (void)settingClick
{
    NSLog(@"%s",__func__);
}
```

- 从上面也可以看出来：这样写的话，代码量就多了，所以要进行代码的封装
----------------------------------------------------------------


---------------------------------------------------------------
####第一种方式

- **新建一个ItemTool类**
+ CYItemTool.h文件中

```objc
#import <UIKit/UIKit.h>

@interface CYItemTool : NSObject
+ (UIBarButtonItem *)itemWithImage:(NSString *)image highImage:(NSString *)highImage target:(id)target action:(SEL)action;
@end
```

  + CYItemTool.m文件中

```objc
#import "CYItemTool.h"

@implementation CYItemTool
+ (UIBarButtonItem *)itemWithImage:(NSString *)image highImage:(NSString *)highImage target:(id)target action:(SEL)action
{
    UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
    [button setBackgroundImage:[UIImage imageNamed:image] forState:UIControlStateNormal];
    [button setBackgroundImage:[UIImage imageNamed:highImage] forState:UIControlStateHighlighted];
    [button sizeToFit];
    [button addTarget:target action:action forControlEvents:UIControlEventTouchUpInside];

    return [[UIBarButtonItem alloc] initWithCustomView:button];
}
@end
```
+ 因为这个抽取的工具类在项目中很多地方基本都能用到，所以写入到pch文件之中
+ pch文件中

```objc
#ifndef ___________0901_PrefixHeader_pch
#define ___________0901_PrefixHeader_pch
#import <UIKit/UIKit.h>
#import "CYItemTool.h"

// Include any system framework and library headers here that should be included in all compilation units.
// You will also need to set the Prefix Header build setting of one or more of your targets to reference this file.
// 日志输出
#ifdef DEBUG // 开发阶段 -DEBUG阶段：使用Log
#define CYLog(...) NSLog(__VA_ARGS__)
#else // 发布阶段——上线阶段：移除Log
#define CYLog(...)
#endif

// 方法输出
#define CYLogFunc CYLog(@"%s",__func__)

#endif
```

####第二种方式
**新建一个扩展类（更推荐这种）**
- 也就是对UIBarButtonItem这个类扩充方法

- UIBarButtonItem+CYExtension.h文件中：

```objc

#import <UIKit/UIKit.h>

@interface UIBarButtonItem (CYExtension)
+ (instancetype)itemWithImage:(NSString *)image highImage:(NSString *)highImage target:(id)target action:(SEL)action;
@end
```
- UIBarButtonItem+CYExtension.m文件中

```objc
#import "UIBarButtonItem+CYExtension.h"

@implementation UIBarButtonItem (CYExtension)
+ (instancetype)itemWithImage:(NSString *)image highImage:(NSString *)highImage target:(id)target action:(SEL)action
{
    {
        UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
        [button setBackgroundImage:[UIImage imageNamed:image] forState:UIControlStateNormal];
        [button setBackgroundImage:[UIImage imageNamed:highImage] forState:UIControlStateHighlighted];
        [button sizeToFit];
        [button addTarget:target action:action forControlEvents:UIControlEventTouchUpInside];

        return [[self alloc] initWithCustomView:button];
    }
}

@end
```

- 同样将头文件导入到pch文件中去

```objc
#ifndef ___________0901_PrefixHeader_pch
#define ___________0901_PrefixHeader_pch
#import <UIKit/UIKit.h>
#import "UIBarButtonItem+CYExtension.h"

// Include any system framework and library headers here that should be included in all compilation units.
// You will also need to set the Prefix Header build setting of one or more of your targets to reference this file.
// 日志输出
#ifdef DEBUG // 开发阶段 -DEBUG阶段：使用Log
#define CYLog(...) NSLog(__VA_ARGS__)
#else // 发布阶段——上线阶段：移除Log
#define CYLog(...)
#endif

// 方法输出
#define CYLogFunc CYLog(@"%s",__func__)

#endif
```
----------------------------------------------------------------






----------------------------------------------------------------
- 那么在封装后我们在每个控制器去设置它们各自的BarButtonItem的时候，就可以这么去设置了
- 上面图中的例子

```objc
#import "CYMeViewController.h"
#import "CYSettingViewController.h"

@interface CYMeViewController ()

@end

@implementation CYMeViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    self.navigationItem.title = @"我的";

    // 我的导航栏右边的内容
    UIBarButtonItem *moonButton = [UIBarButtonItem itemWithImage:@"mine-moon-icon" highImage:@"mine-moon-icon-click" target:self action:@selector(moonClick)];
    UIBarButtonItem *settingButton = [UIBarButtonItem itemWithImage:@"mine-setting-icon" highImage:@"mine-setting-icon-click" target:self action:@selector(settingClick)];

    self.navigationItem.rightBarButtonItems = @[settingButton,moonButton];
}
- (void)moonClick
{
    CYLogFunc
}


- (void)settingClick
{
    CYSettingViewController *setting = [[CYSettingViewController alloc] init];
    [self.navigationController pushViewController:setting animated:YES];
}

@end
```
- 在另外一个控制器的设置，同样

```objc
#import "CYFocusViewController.h"

@interface CYFocusViewController ()

@end

@implementation CYFocusViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    self.navigationItem.title = @"我的关注";

    // 设置导航栏左边的内容
    self.navigationItem.leftBarButtonItem = [UIBarButtonItem itemWithImage:@"friendsRecommentIcon" highImage:@"friendsRecommentIcon-click" target:self action:@selector(CYFocusViewClick)];
}
- (void)CYFocusViewClick
{
    CYLogFunc
    UITableViewController *focus = [[UITableViewController alloc] init];
    focus.navigationItem.title = @"百科不是全书";
    [self.navigationController pushViewController:focus animated:YES];

}
```
- 这样就方便多了，直接调用方法，也不用担心会写错

- **个人愚见--不管是新建一个类还是扩展一个类，都是差不多的，也看个人喜好。但是实际开发中，类方法的扩展可能有成百上千个的时候，你一个个新建类的话，不太现实，所以为了追求最优化方案还是采用第二种方法吧！至少好记好用点吧！**

- 稍后的时间，我会尽量将一些封装的东西都整理出来，敬请关注！

- 下面的内容，感兴趣的朋友可以研究一下！
--------------------------------------------------------------
####扩充说明--类别
- **网上copy过来的，有助于大家理解为什么选用类别优于新建一个类**

- **下面文字copy源于：http://www.2cto.com/kf/201502/376993.html**

- **原文内容如下：**
- 类别是一种为现有的类添加新方法的方式
- 利用Objective-C的动态运行时分配机制，可以为现有的类添加新方法，这种为现有的类添加新方法的方式称为类别catagory。
- 他可以为任何类添加新的方法，包括那些没有源代码的类。
- 类别使得无需创建对象类的子类就能完成同样的工作


####创建类别
- 1、声明类别声明类别与声明类的形式很相似

```objc
@interface NSString(NumberConvenience)-(NSNumber *)lengthAsNumber;@end//NumberConvenience
```
- 这个声明有两个特点：
   + (1)现有的类位于@interface关键字之后，其后是位于圆括号中的类别名称。
         + 类别名称是NumberConvenience
         + 而且该类别将向NSString类中添加方法
         + 换句话说：“我们向NSString类中添加一个名称为NumberConvenience的类别。
         + 同名类别有唯一性，但是可以添加任意多的不同名类别。
   + (2)可以执行希望向其添加类别的类以及类别的名称，还可以列出添加的方法不可以添加新的实例变量，类别生命中没有实例变量部分。


- 2、实现类别

```objc
@implementation NSString(NumberConvenience)-(NSNumber *)lengthAsNumber{unsigned int length = [self length];return ([NSNumber numberWithUnsignedInt : length]);} **//lengthAsNumber**@end **//NumberConvenience
```
   + 在实现部分也包括类名、类别名和新方法的实现代码

- 3、类别的局限性有两方面局限性：
   + (1)无法向类中添加新的实例变量，类别没有位置容纳实例变量。
   + (2)名称冲突，即当类别中的方法与原始类方法名称冲突时，类别具有更高的优先级。类别方法将完全取代初始方法从而无法再使用初始方法。无法添加实例变量的局限可以使用字典对象解决

- 4、类别的作用
   + 类别主要有3个作用：
        + (1)将类的实现分散到多个不同文件或多个不同框架中。
        + (2)创建对私有方法的前向引用。
        + (3)向对象添加非正式协议。 继承可以增加，修改或者删除方法，并且可以增加属性。


####另外一篇

- **原文地址：http://www.cnblogs.com/visen-0/archive/2012/03/02/2376834.html**

####类别
-    有时我们需要在一个已经定义好的类中增加一些方法，而不想去重写该类。比如，当工程已经很大，代码量比较多，或者类中已经包住很多方法，已经有其他代码调用了该类创建对象并使用该类的方法时，可以使用类别对该类扩充新的方法。
  注意：类别只能扩充方法，而不能扩充成员变量。
 
-   实例分析：
     + 1、目的：在我的工程中，我需要对图片进行压缩，此时我想到类别，利用类别对UIImage类进行扩展，增加图片压缩方法。
     + 2、类别定义：

- 类别.h声明文件
 
```objc
#import <Foundation/Foundation.h>
 
@interface UIImage (UIImageExt)
//这个方法就是我添加的图片压缩的方法
- (UIImage*)imageByScalingAndCroppingForSize:(CGSize)targetSize;
@end
 
 ```
- 类别.m实现文件
 
```objc 
#import "UIImageExt.h"
 
@implementation UIImage (UIImageExt)
 
- (UIImage*)imageByScalingAndCroppingForSize:(CGSize)targetSize
{
UIImage *sourceImage = self;
UIImage *newImage = nil;
CGSize imageSize = sourceImage.size;
CGFloat width = imageSize.width;
CGFloat height = imageSize.height;
CGFloat targetWidth = targetSize.width;
CGFloat targetHeight = targetSize.height;
CGFloat scaleFactor = 0.0;
CGFloat scaledWidth = targetWidth;
CGFloat scaledHeight = targetHeight;
CGPoint thumbnailPoint = CGPointMake(0.0,0.0);
 
if (CGSizeEqualToSize(imageSize, targetSize) == NO)
{
CGFloat widthFactor = targetWidth / width;
CGFloat heightFactor = targetHeight / height;
 
if (widthFactor > heightFactor)
scaleFactor = widthFactor; // scale to fit height
else
scaleFactor = heightFactor; // scale to fit width
scaledWidth= width * scaleFactor;
scaledHeight = height * scaleFactor;
 
// center the image
if (widthFactor > heightFactor)
{
thumbnailPoint.y = (targetHeight - scaledHeight) * 0.5;
}
else if (widthFactor < heightFactor)
{
thumbnailPoint.x = (targetWidth - scaledWidth) * 0.5;
}
}
 
UIGraphicsBeginImageContext(targetSize); // this will crop
 
CGRect thumbnailRect = CGRectZero;
thumbnailRect.origin = thumbnailPoint;
thumbnailRect.size.width= scaledWidth;
thumbnailRect.size.height = scaledHeight;
 
[sourceImage drawInRect:thumbnailRect];
 
newImage = UIGraphicsGetImageFromCurrentImageContext();
if(newImage == nil)
NSLog(@"could not scale image");
 
//pop the context to get back to the default
UIGraphicsEndImageContext();
return newImage;
}
 
@end
```

- 3、如何使用类别
    + 在上文我已经对UIImage进行了扩展，下面如何将演示在我的工程中如何调用该方法：

```objc
//根据图片tag显示图片
-(void)showPhotoBySerialNumber:(int)imageTag;
{
//这个largeImageArray是NSMutableArray类型的，存放图片存储路径，根据路径得到UIImage
UIImage *img = [UIImage imageWithContentsOfFile:[self.largeImageArray objectAtIndex:imageTag]];
//MyScrollView是我自定义的ScrollView，目的是使ScrollView响应点击事件，关于如何自定义的ScrollView在以后的博客中，我将会阐述
MyScrollView *scrView = [[MyScrollView alloc] initWithFrame:CGRectMake(340*imageTag, 0, 320, 480)];
scrView.host = self;
//这句就是调用了类别，通过UIImage实例对象，调用imageByScalingAndCroppingForSize:类别
scrView.image = [img imageByScalingAndCroppingForSize:CGSizeMake(320.0, 480.0)];
scrView.tag = imageTag+100;
//下面这句，就是把上面的scrView塞到imageScrollView上，imageScrollView是UIScrollView类型
[self.imageScrollView addSubview:scrView];
[scrView release];
 
}
``` 

####二、协议
- 协议(protocol)类似于java语言里的接口(interface)，定义了一组方法，而不提供具体实现， 只有那些“遵守”(conform to)或“采用”(adopt)了这些Protocol的类来给出自己的实现。
- 协议不是类本身，它们仅定义了其它对象有责任实现的接口。当在自己的类中实现协议的方法时，用户的类就是遵守这个协议的，协议声明的方法可以被任何一个类实现。

- 1、协议的语法结构如下：

```objc
@protocol ProtocolName   //协议名
   methodDeclaration;        //方法名  @end
```
- 2、如何使用协议
   + 而在类声明时，语法如下：
 
```objc
 @interface ClassName : ParentClassName < ProtocolName>
```  
 
-  然后在该类的实现文件中，实现该协议的方法methodDeclaration
 

####实例分析
- 下面以在我的工程项目中协议的使用方法，这里只要讲我自己定义的协议。
    + 1）该实例的目的：使在ScrollView上面的UIImageView响应点击事件
    + 2）协议定义：
 

- 在类的.h声明文件中定义协议
 
```objc 
#import <Foundation/Foundation.h>
//协议ImageTouchDelegate
@protocol ImageTouchDelegate
//协议中声明的方法
-(void)imageTouch:(NSSet *)touches withEvent:(UIEvent *)event whichView:(id)imageView;
@end
 
@interface ImageTouchView : UIImageView {
id<ImageTouchDelegate>  delegate;
}
@property(nonatomic,assign)id<ImageTouchDelegate> delegate;
@end
``` 
 
- 该类的.m实现文件如下

```objc 
#import "ImageTouchView.h"
 
 
@implementation ImageTouchView
@synthesize delegate;
 
-(id)initWithFrame:(CGRect)frame
{
if (self == [super initWithFrame:frame])
{
[self setUserInteractionEnabled:YES];
}
return  self;
}
-(BOOL)touchesShouldBegin:(NSSet *)touches withEvent:(UIEvent *)event inContentView:(UIView *)view
{
return YES;
}
 
-(void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
[delegate imageTouch:touches withEvent:event whichView:self];
}
 
@end
``` 

-  3）如何使用协议？
      + 前面我已经定义了ImageTouchDelegate，那么如何使用该协议的方法呢？              

      + 当某个类要采用该协议时，在类的声明中列出协议的名称即可，如果要采用多个协议时，在后面的尖括号中以逗号分隔，加入即可。
      + 当在类采用了该协议时，就要在除了.h文件中进行声明外，还必须在它的.m实现文件中对声明的协议中的方法进行实现。还以上述我们已经定义好的协议为例，演示在我的工程中如何实现该协议。

- 在我的类中，首先在.h声明文件中，采用协议ImageTouchDelegate
 
```objc 
#import <UIKit/UIKit.h>
//引入定义协议ImageTouchDelegate的头文件
#import "ImageTouchView.h"
//把协议名放到父头后面的尖括号里面，如果有多个协议，用逗号分隔
@interface PhotoOnShotViewController : UIViewController<ImageTouchDelegate> {
 
}
 
@end
``` 
 
- 下面要在类的.m实现文件中实现该协议定义的方法
 
```objc
#import "PhotoOnShotViewController.h"
 
@implementation PhotoOnShotViewController
 
 
 
//实现协议中定义的方法，
-(void)imageTouch:(NSSet *)touches withEvent:(UIEvent *)event whichView:(id)imageView{
 
}
```







