# Objective-C Style Guide

这篇风格指南概括了「纽约时代周刊」工作的 iOS 团队 raywenderlich.com 的编码约定。我们非常欢迎在 [issues](https://github.com/jkyin/Objective-C-Style-Guide/issues/new) 和 [pull requests](https://github.com/jkyin/Objective-C-Style-Guide/pulls) 上给我们反馈。

## 介绍

这里是一些来自 Apple 的风格指南文档。如果某些没有在本文提及，那在下面这些文档中的某篇会有详细说明：

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## 目录

* [语言](#语言)
* [组织代码](#组织代码)
* [点语法](#点语法)
* [空格](#空格)
* [条件语句](#条件语句)
  * [三元运算符](#三元运算符)
* [错误处理](#错误处理)
* [方法](#方法)
* [变量](#变量)
  * [变量修饰符](#变量修饰符) 
* [命名](#命名)
  * [下划线](#下划线)
* [注释](#注释)
* [init 和 dealloc](#init-和-dealloc)
* [文字变量](#文字变量)
* [CGRect 函数](#cgrect-函数)
* [常量](#常量)
* [枚举类型](#枚举类型)
* [Bitmasks](#bitmasks)
* [Case 语句](#case-语句)
* [私有属性](#私有属性)
* [图片命名](#图片命名)
* [布尔值](#布尔值)
* [单例](#单例)
* [Xcode 工程](#xcode-工程)

## 语言

要求使用美式英语。

**推荐：**

```objectivec
UIColor *myColor = [UIColor whiteColor];
```

**不推荐：**

```objectivec
UIColor *myColour = [UIColor whiteColor];
```

## 组织代码

使用 `#pragma mark -` 来把方法按功能分类，协议 / 委托也使用这个基本结构。

```objectivec
#pragma mark - Lifecycle

- (instancetype)init {}
- (void)dealloc {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

#pragma mark - IBActions

- (IBAction)submitData:(id)sender {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## 点语法

点语法应该**总是**被用来访问要可变属性。中括号语法优先用于其他所有的实例对象。

**例如：**

```objectivec
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**而不是：**

```objectivec
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## 空格

* 使用 4 个空格缩进。永远不要使用 Tab 键。确保在 Xcode 设置中选中此偏好。
* 方法间的花括号和其他类型的括号 (`if`/`else`/`switch`/`while` 等等) 应该始终另起一行。

**例如：**

```objectivec
if (user.isHappy) 
{
	//Do something
} else {
	//Do something else
}
```

* 不同的方法之间按照视觉上的一目了然的话，应该是恰好一个空行。在方法的内部应该使用空格来分割功能块，如果要经常这样做的话那你可能有必要去封装一个新的方法了。
* 推荐使用自动合成功能。但是如果有需要的话，`@synthesize` and `@dynamic` 应该在实现文件里互起一行声明。
* 要避免经常性的使用「冒号对齐」的方法调用。当一个方法名有 >= 3 个冒号的时候再使用「冒号对齐」，这样就能让代码更可读。请永远**不要**在包含 block 语句的方法里使用「冒号对齐」，因为这样会使代码变得难以辨认。

**推荐：**

```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
	// something
} completion:^(BOOL finished) {
	// something
}];
```

**不推荐：**

```objc
// colon-aligning makes the block indentation hard to read
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```

## 条件语句

条件语句应该总是使用括号来包含条件主体（即使它只有一行），如果不写入括号中会出现[错误](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256)。这个错误会把第二行代码包含进 if 语句中。另一方面，在 if 语句内部被注释掉的话还会有[更危险的缺陷会发生](http://programmers.stackexchange.com/a/16530)，下一行就会毫无察觉地成了 if 语句的一部分。此外，这种风格与其他表达式语句一起具有更好的一致性，并且因此更易读。

**例如：**

```objectivec
if (!error) {
    return success;
}
```

**而不是：**

```objectivec
if (!error)
    return success;
```

或者

```objectivec
if (!error) return success;
```

### 三元运算符

三元操作符应该只被用来提高代码的简洁性。单条件语句通常还有待评价。多重条件语句通常比 if 语句，或重构实例变量时更易于理解。

**例如：**

```objectivec
result = a > b ? x : y;
```

**而不是：**

```objectivec
result = a > b ? x = c > d ? c : d : y;
```

## 错误处理

当方法通过引用返回一个 error 参数的时候，要转换为返回的值，而不是 error 变量。

**例如：**

```objectivec
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**而不是：**

```objectivec
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## 方法

在方法声明中，应该在（-/+ 符号）的后面留一个空格。在方法的分段处也应该留一个空格。

**例如：**:

```objectivec
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

## 变量

变量应该尽可能的用描述性的语言命名。应该避免使用单字母来命名变量，除了用于 `for()` 循环。

星号表示变量是一个指针，例如 `NSString *text` 不要写成 `NSString* text`。也不见意使用 `NSString * text`，除非是在定义常量的情况下。

属性应该无论在何时都应该是用来代替很直白的实例变量。应该避免直接使用实例变量，除非在初始化方法中（`init`, `initWithCoder:`，等等），`dealloc` 方法和自定义的 `setter` 和 `getter` 方法中。想要了解更多关于在初始化方法和 dealloc 中存储器方法信息请访问[这里](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6)。

**例如：**

```objectivec
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**而不是：**

```objectivec
@interface NYTSection : NSObject {
    NSString *headline;
}
```

### 变量修饰符

当要[引入 ARC ](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4)使用变量修饰符的时候，修饰符（`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`）应该放在变量名和星号之间，例如：`NSString * __weak text`。

## 命名

Apple 命名规范无论何时都应该被遵循，尤其是那些跟[内存管理规则](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508))相关的。

长的，描述性的方法和变量名才是被推荐的。

**例如：**

```objectivec
UIButton *settingsButton;
```

**而不是：**

```objectivec
UIButton *setBut;
```

三个字母的前缀（例如，`NYT`）应该用于类名和常量，然后 Core Data 的实体名应该要忽略前缀。常量应该使用驼峰式命名规范，要与类名有相关性要字如其意，并且所有单词的开头字母和前缀都应该大写，

**例如：**

```objectivec
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**而不是**

```objectivec
static const NSTimeInterval fadetime = 1.7;
```

属性和局部变量应该使用开头字母小写的驼峰式命名规范。

实例变量应该使用开头字母小写的驼峰式命名规范，并且前缀是下划线。这与 LLVM 自动合成的实例变量一致。**如果 LLVM 能够自动合成实例变量那就不要手动的去操作**。

**例如：**

```objectivec
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**而不是：**

```objectivec
id varnm;
```

### 下划线

当使用属性的时候，实例变量应该总是能够通过 `self.` 来访问和改变值。这样做能够使所有的属性在视觉上变得更明显。

一个例外情况是：在初始化程序内部，辅助实例变量（例如：_variableName）应该直接用来防止任何 getters/setters 潜在的副作用。

局部变量不应该包含下划线。

## 注释

当需要注释的时候，注释应该是用来解释**为什么**的，即这块代码做了哪些事情的详细说明。任何注释要么是最新的要么就删掉。

应该避免使用块注释，并且尽可能的让代码易读，只在有需要的时候用一两行来解释代码做了什么。这项说明不适用于需要用来生成文档的注释。

## init 和 dealloc

`dealloc` 方法应该放在 `implementation` 的上面，紧跟着 `@synthesize` 和 `@dynamic` 语句的后面。任何类的 `init` 应该直接放在 `dealloc` 方法的下面。

`init` 方法的结构应该是这样的：

```objectivec
- (instancetype)init 
{
    self = [super init]; // or call the designated initalizer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

## 文字变量

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.
`NSString`, `NSDictionary`, `NSArray`, 和 `NSNumber` 这些文字变量无论在何时都应该被用来创建不可变的实例对象。特别要留意 `nil` ，不要把它传入到了 `NSArray` 和 `NSDictionary` 中，因为这样会导致崩溃。

**例如：**

```objectivec
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**而不是**

```objectivec
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect 函数

当访问 `x`, `y`, `width`，或 `CGRect` 中的 `height`，总是应该使用 `CGGeometry` 函数，而不应该直接访问结构的成员。来自 Apple 的 [CGGeometry 文档](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) 引用：

> 在计算那些矩形的结果之前，这篇参考中所提到的所有使用 CGRect 数据结构的函数会使用隐式输入规范。由于这个原因，你的应用应该避免直接读写存储在 CGRect 数据结构中的数据。相反，使用这里的函数写法来操作矩形和检索它们的特征。

**例如：**

```objectivec
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**而不是：**

```objectivec
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## 常量

常量比一行数字或文字更好，因为它们能够很容易的被各种变量复制，还可以快速地在不必找到位置再替换的条件下改变值。应该使用 `static` 来定义常量，并且不要使用 `#define` 除非明确表明是作为一个宏。

**例如：**

```objectivec
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**而不是：**

```objectivec
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## 枚举类型

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`
当使用 `enum` 的时候，通常建议新的固定基础类型规范，因为它具有更强的类型检查和代码自动化。现在的 SDK 已包含了一个宏来来很方便的鼓励固定基础类型的使用 - `NS_ENUM()`。

**例如：**

```objectivec
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## Bitmasks

When working with bitmasks, use the `NS_OPTIONS` macro.

**Example:**

```objectivec
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
  NYTAdCategoryAutos      = 1 << 0,
  NYTAdCategoryJobs       = 1 << 1,
  NYTAdCategoryRealState  = 1 << 2,
  NYTAdCategoryTechnology = 1 << 3
};
```
## Case 语句

对于 case 语句来说，花括号没必要，除非编译器强制要求。  
当一个 case 包含有多行的时候，那必须添加花括号。

**例如：**

```objectivec
switch (condition) {
  case 1:
    // ...
    break;
  case 2: {
    // ...
    // 在多行代码时使用花括号
    break;
  }
  case 3:
    // ...
    break;
  default: 
    // ...
    break;
}
```

有时相同的代码能够用于多种情况下，这时应该使用瀑布流的方式。瀑布流指的是移除了 `break` 语句的情况，因此可以允许执行流传递到下一个 case 的值。瀑布流的注释应该一目了然。


```objectivec
switch (condition) {
	case 1:
    	// ** 瀑布流！**
	case 2:
    	// code executed for values 1 and 2
    	break;
	default: 
    	// ...
    	break;
}
```

当在 switch 中使用枚举类型的时候，`default` 并不需要。例如：

```objectivec
RWTLeftMenuTopItemType menuType = RWTLeftMenuTopItemMain;

switch (menuType) {
	case RWTLeftMenuTopItemMain:
		// ...
		break;
	case RWTLeftMenuTopItemShows:
    	// ...
    	break;
	case RWTLeftMenuTopItemSchedule:
    	// ...
    	break;
}
```

## 私有属性

私有属性应该在类的实现文件的类扩展处声明（匿名类别）。永远不要使用命名类别（例如：`NYTPrivate` 或 `private`），除非是用来扩展另一个类。

**例如：**

```objectivec
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 图片命名

图片名字应该与组织和开发者的本意保持一致。它们应该使用驼峰式命名规范，并且使用一个能够描述它们用途的文字来命名。使用无前缀的类名或自定义的属性名开始，然后紧接着是颜色或布局的描述，最后是它们的状态描述。

**例如：**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` 和 `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` 和 `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

相似用途的图片应该使用文件夹组织起来。

## 布尔值

既然 `nil` 等同于 `NO` 那就没有必要在条件语句中进行比较。永远不要直接把某些东西与 `YES` 相比较，因为 `YES` 的定义等同于 1 并且一个 `BOOL` 相当于 8 位。

这样一来能让整个文件保持一致性，并且具有逻辑性和可读性更好。

**例如：**

```objectivec
if (!someObject) {
}
```

**而不是：**

```objectivec
if (someObject == nil) {
}
```

-----

**这里是两个 `BOOL` 的例子**

```objectivec
if (isAwesome)
if (![someObject boolValue])
```

**而不是：**

```objectivec
if (isAwesome == YES) // 绝不要这样做
if ([someObject boolValue] == NO)
```

-----

`BOOL` 属性的名字应该取名为形容词，属性名可以忽略 'is' 前缀，但 `get` 访问方法要使用传统的命名，例如：

```objectivec
@property (assign, getter=isEditable) BOOL editable;
```

示例参考来自 [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)。

## 单例

单例对象应该使用安全线程机制来创建它们的共享实例。

```objectivec
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```

这能够防止[某些时候可能出现各种程序崩溃的问题](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html)。

## Xcode 工程

为了防止文件散乱，实际文件应该要与 Xcode 工程里的文件保持同步。任何 Xcode 分组的创建都应该能够在文件系统里体现出来。代码文件不仅仅单一地根据类型来分组，而且还可以按更明显的用途来分组。

如果可能的话，请总是在 target's Build Settings 中保持 "Treat Warnings as Errors" 的开启还有尽可能的启用 [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings)。如果你需要忽略特定的 warning，请使用 [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas)。

# 其他的 Objective-C 风格指南

If ours doesn't fit your tastes, have a look at some other style guides:

* [New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
