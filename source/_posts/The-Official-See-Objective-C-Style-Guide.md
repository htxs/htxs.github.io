title: The Official See Objective-C Style Guide
date: 2015-10-20 19:57:59
categories: iOS
tags: [团队, 风格]
---

# See 官方 Objective-C 风格指南

该代码风格指南适用于 See iOS 研发组。我们做这个风格指南的原因是，iOS 组项目成员逐渐增长。为了保持代码的一致和美观，便于代码的易读性和可维护性。我们参考了 [raywenderlich.com 官方风格指南](https://github.com/raywenderlich/objective-c-style-guide)。

## 目录

* [代码组织](#代码组织)
* [空格](#空格)
* [注释](#注释)
* [命名](#命名)
* [下划线](#下划线)
* [点运算符](#点运算符)
* [方法](#方法)
* [属性与变量](#属性与变量)
* [属性修饰符](#属性修饰符)
* [字面量](#字面量)
* [常量](#常量)
* [枚举类型](#枚举类型)
* [Case](#case)
* [布尔值](#布尔值)
* [条件语句](#条件语句)
* [初始化方法](#初始化方法)
* [单例](#单例)

## 代码组织

使用 `#pragma mark -` 将相关联的方法、协议/代理实现组织为分组，采用以下的通用结构。

```objc
#pragma mark - Lifecycle

- (void)dealloc {}
- (instancetype)init {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors

- (id)customProperty {}
- (void)setCustomProperty:(id)value {}

#pragma mark - Public Methods

- (void)publicMethod {}

#pragma mark - NSNotification
#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - GestureRecognizer

- (void)handleTapGesture:(UITapGestureRecognizer *)sender {}

#pragma mark - IBActions

- (IBAction)submitData:(id)sender {}

#pragma mark - Private Methods

- (void)privateMethod {}

```

## 空格

* 使用 4 个空格符作为缩进。
* 方法的大括号应该与方法名左开在同一行，换行右闭。
* `if`/`else`/`switch`/`while` 等控制操作符的大括号规则与方法的大括号规则基本一致，但多个分支时，`else`/`else if` 需要换行。

**Preferred:**
```objc
-  (void)someMethod:(User *)user {
    if (user.isHappy) {
        //Do something
    }
    else {
        //Do something else
    }
}

```

**Not Preferred:**
```objc
-  (void)someMethod:(User *)user
{
    if (user.isHappy)
    {
        //Do something
    }
    else
    {
        //Do something else
    }
}
```

* 方法声明及方法调用时，方法名之间的空格应该严格按照 [方法](#方法) 处的代码规范进行编码。
* 尽量避免以冒号对齐的方式调用方法，但方法名过长或者超过 (>=) 3 个冒号时，使用冒号对齐来调用该方法可以使代码更易读。**注：** 由于 Xcode 对于 Block 代码块的缩进使得代码难读，所以不要使用冒号对齐的方式来调用此类方法。

**Preferred:**

```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
    // something
} completion:^(BOOL finished) {
    // something
}];
```

**Not Preferred:**

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

## 注释

只在需要解释为何需要此代码段时，才使用注释。并且保持注释的同步更新，不需要时则删除之。

尽量做到代码即注释，所以应该避免使用块状的注释。通常只需要单行或者少数几行的注释说明该代码段的作用/注意点即可，此时应该使用 `// ` 进行注释。**例外：** 需要生成文档时，使用块状注释。

## 命名

尽量遵循苹果的命名约定，特别是跟 [内存管理](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)) 相关的规则。

带自描述的方法名或变量名总是比较好的，尽管它比较冗余。

**Preferred:**

```objc
UIButton *settingsButton;
```

**Not Preferred:**

```objc
UIButton *setBut;
```

类名以及常量应该带上具有标志性的前缀，于我们公司而言，'See' 就是不错的选择。

常量应该采用驼峰形式，将所有首字母大写，并且带上相关联的类名更清晰地表明场景用途。

**Preferred:**

```objc
static CGFloat const kPOPAnimationDurationDefault = 0.4;
```

**Not Preferred:**

```objc
static CGFloat const animationTime = 0.4;
```

属性也是采用驼峰形式，只是首字母小写。对于属性应该尽量使用自动合成而非手动使用 @synthesize 语句合成，除非在需要的情况下。

**Preferred:**

```objc
@property (nonatomic, strong) NSString *descriptiveVariableName;
```

**Not Preferred:**

```objc
id varnm;
```

## 下划线

使用属性时，尽量使用点运算符 `self.` ，因为有时候在 getters/setters 中有特殊的处理，例如延迟加载等。

但是在初始化方法、getters/setters 方法体内需要谨慎使用，避免潜在的负面效果，例如方法循环调用等。

局部变量不需要使用下划线（部分开发者定义局部变量时，喜欢以下划线开头）。项目中常用的 `weak_self` 也建议慢慢修改为 `weakSelf`

## 点运算符

点运算符是纯粹地封装了方法调用，便于访问。当使用点运算符时，属性仍然是通过 getter 和 setter 进行访问和设值的。更多信息请查看 [此处](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html) 文档。

为了使代码更加简洁，应该 **始终** 使用点运算符访问属性和为属性设值，其它类型的方法调用则采用中括号的形式。

**Preferred:**
```objc
NSInteger arrayCount = [self.array count];
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not Preferred:**
```objc
NSInteger arrayCount = self.array.count;
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## 方法

方法声明中，空格符的要求：

* 方法类型（-/+ 符号）前不需要空格
* 方法类型（-/+ 符号）后需要一个空格
* 返回值与方法名之间不需要空格
* 方法名与冒号分隔符之间不需要空格
* 冒号与参数类型之间不需要空格
* 参数类型与参数名之间不需要空格
* 参数类型，指针标识符 `*` 与类型名之间需要一个空格
* 方法名的各分段之间需要一个空格

"and" 是保留词，不应该单独使用它作为多参数的分段方法名。下面的例子中可以看出空格的使用规范，方法名的命名规范等。

**Preferred:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void) sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView: (NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat) width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## 属性与变量

业界中关于属性与实例变量的争论一直都存在，各有拥护者，我们就不参与争论了。我更坚持使用属性，除非你有更好的理由使用实例变量。

**Preferred:**

```objc
@interface SeeThemeModel : NSObject

@property (copy, nonatomic) NSString *themeTitle;

@end
```

**Not Preferred:**

```objc
@interface SeeThemeModel : NSObject {
    NSString *themeTitle;
}
```

## 属性修饰符

考虑到从 Interface Builder 连接 IBOutlet 时自动产生的代码中，属性修饰符的顺序是内存管理在先，原子性在后，所以优先使用该顺序。再考虑到原子性在先，内存管理在后的话，可以更好的代码对齐，所以此顺序也是可以接受的。只需要保证同一个文件的属性修饰符顺序一致即可。

**Preferred:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
```

**Not Preferred:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (nonatomic, strong) NSString *tutorialName;
```

属性类型包括可变和不可变版本时（例如：NSString），优先使用 `copy` 而不是 `strong`。为什么呢？因为你即使定义了 `NSString` 的属性，但外界仍然可以传递 `NSMutableString` 的实例，所以外界可能会修改该属性，而你很难察觉。

**Preferred:**

```objc
@property (copy, nonatomic) NSString *tutorialName;
```

**Not Preferred:**

```objc
@property (strong, nonatomic) NSString *tutorialName;
```

## 字面量

`NSString`，`NSDictionary`，`NSArray`，和 `NSNumber` 可以使用字面量创建不可变的实例对象。特别注意：使用 `NSArray` 和 `NSDictionary` 的字面量时，值为 `nil` 时，会导致崩溃。

**Preferred:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
```

**Not Preferred:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
```

## 常量

常量用于定义在程序运行期间不被修改的、固定的值，便于修改相关联的值时，无需查找和替换项目中各处的代码。虽然使用 `#define` 可以达到相同的目的，但宏定义方式没有强类型，而 `static` `const` 具有强类型，具有类型安全的优势。所以应该使用 `static` `const` 定义常量。

**Preferred:**

```objc
static NSString * const SeeAboutViewControllerCompanyName = @"See";

static CGFloat const kPOPAnimationDurationDefault = 0.4;
```

**Not Preferred:**

```objc
#define CompanyName @"See"

#define defaultAnimationDuration 0.4
```

## 枚举类型

当定义枚举类型时，推荐使用新的枚举类型定义语法 `NS_ENUM`，因为编译器会对它进行强类型检查。

**For Example:**

```objc
typedef NS_ENUM(NSInteger, SeeSharePlatformType) {
    SeeSharePlatformTypeWeixin,
    SeeSharePlatformTypeQQ,
    SeeSharePlatformTypeWeibo
};
```

`NS_ENUM` 也可以指定枚举值。

```objc
typedef NS_ENUM(NSInteger, TTTAttributedLabelVerticalAlignment) {
    TTTAttributedLabelVerticalAlignmentCenter   = 0,
    TTTAttributedLabelVerticalAlignmentTop      = 1,
    TTTAttributedLabelVerticalAlignmentBottom   = 2
};
```

旧式的枚举定义 `enum` 不再建议使用，除非用于 CoreFoundation C 代码中。

**Not Preferred:**

```objc
enum GlobalConstants {
    kMaxPinSize = 5,
    kMaxPinCount = 500,
};
```

## Case

Case 语句的括号不是必须的，当 case 语句只包含一行时，可以不用括号。当 case 语句包含多行时，需要加上括号。

```objc
switch (condition) {
    case 1:
        // ...
        break;
    case 2: {
        // ...
        // Multi-line example using braces
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

当多个 case 语句等效，需要多个 case 语句执行相同的代码块时，去掉对应的 "break" 语句即可。此时应该清晰的注释为何此处需要等效处理，避免后期无法维护。

```objc
switch (condition) {
    case 1:
        // ** fall-through! **
    case 2:
        // code executed for values 1 and 2
        break;
    default: 
        // ...
        break;
}
```

使用枚举类型作为 "switch" 条件时，不需要 "default" 语句。

```objc
SeeSharePlatformType platformType = SeeSharePlatformTypeWeixin;

switch (platformType) {
    case SeeSharePlatformTypeWeixin:
        // ...
        break;
    case SeeSharePlatformTypeQQ:
        // ...
        break;
    case SeeSharePlatformTypeWeibo:
        // ...
        break;
}
```

## 布尔值

Objective-C 中采用 `YES` 和 `NO`。而 `true` 和 `false` 只用于 CoreFoundation，C 或者 C++ 中。

因为 `nil` 和 `NO` 等价，所以没有必要在条件语句中比较 `nil` 和 `NO`。

不要将其它类型的值与 `YES` 直接比较，因为 `YES` 定义为 1，而一个 `BOOL` 值可以有 8 bits。

**Preferred:**

```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Not Preferred:**

```objc
if (someObject != nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

如果一个 `BOOL` 类型的属性，可根据情况省略 "is" 前缀，并且指明 getter 方法，比如：

```objc
@property (assign, getter=isEditable) BOOL editable;
```
更多信息请参考 [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)。

## 条件语句

为避免出错，条件语句的方法体应该总是使用大括号包裹，即便方法体中只有一个语句。这样一来，更容易理解所有的条件分支，也更容易发现错误所在。反之，期望在条件语句后追加一句代码，但这句代码其实已经超出了该条件的范围了，很容易触发逻辑错误。这里应该有个 [案例](http://programmers.stackexchange.com/a/16530)。

**Preferred:**
```objc
if (!error) {
    return success;
}
```

**Not Preferred:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

## 初始化方法

初始化方法应遵循苹果的代码模板，返回类型应使用 `instancetype` 而避免使用 `id` 。

```objc
- (instancetype)init {
    self = [super init];
    if (self) {
        // ...
    }
    return self;
}
```

## 单例

单例对象需要采用线程安全的方式创建共享实例，最佳实践是采用 GCD `dispatch_once` 方法创建单例。

```objc
+ (instancetype)sharedInstance {
    static id sharedInstance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        sharedInstance = [[self alloc] init];
    });

    return sharedInstance;
}
```

# 其它的风格指南

* [Raywenderlich.com](https://github.com/raywenderlich/objective-c-style-guide)
* [Robots & Pencils](https://github.com/RobotsAndPencils/objective-c-style-guide)
* [New York Times](https://github.com/NYTimes/objective-c-style-guide)
* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
