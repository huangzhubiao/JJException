[![CocoaPods Compatible](https://img.shields.io/cocoapods/v/JJException.svg)](https://img.shields.io/cocoapods/v/JJException.svg)
[![Build Status](https://travis-ci.org/jezzmemo/JJException.svg?branch=master)](https://travis-ci.org/jezzmemo/JJException.svg?branch=master)
[![codecov](https://codecov.io/gh/jezzmemo/JJException/branch/master/graph/badge.svg)](https://codecov.io/gh/jezzmemo/JJException)
[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Platform](https://img.shields.io/cocoapods/p/JJException.svg?style=flat)](http://cocoadocs.org/docsets/JJException)
![License MIT](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)

# JJException

保护App,一般常见的问题不会导致闪退，增强App的健壮性，同时会将错误抛出来，根据每个App自身的日志渠道记录，下次迭代或者热修复以下问题.

- [x] Unrecognized Selector Sent to Instance

- [x] NSArray,NSMutableArray,NSDictonary,NSMutableDictionary

- [x] KVO

- [x] Zombie Pointer

- [x] NSTimer

- [x] NSNotification

- [x] NSString,NSMutableString,NSAttributedString,NSMutableAttributedString

## 如何安装

__Podfile__

```
pod 'JJException'
```

__Cartfile__

```
github "jezzmemo/JJException"
```

__手动导入代码__

导入`Source`文件夹里所有文件，需要将`MRC`目录下所有.m文件，编译选项更改成-fno-objc-arc

## 如何使用

* 所有异常的分类,根据自身需要，自由组合
```objc
typedef NS_OPTIONS(NSInteger,JJExceptionGuardCategory){
    JJExceptionGuardNone = 0,
    JJExceptionGuardUnrecognizedSelector = 1 << 1,
    JJExceptionGuardDictionaryContainer = 1 << 2,
    JJExceptionGuardArrayContainer = 1 << 3,
    JJExceptionGuardZombie = 1 << 4,
    JJExceptionGuardKVOCrash = 1 << 5,
    JJExceptionGuardNSTimer = 1 << 6,
    JJExceptionGuardNSNotificationCenter = 1 << 7,
    JJExceptionGuardNSStringContainer = 1 << 8,
    JJExceptionGuardAllExceptZombie = JJExceptionGuardUnrecognizedSelector | JJExceptionGuardDictionaryContainer | JJExceptionGuardArrayContainer | JJExceptionGuardKVOCrash | JJExceptionGuardNSTimer | JJExceptionGuardNSNotificationCenter | JJExceptionGuardNSStringContainer,
    JJExceptionGuardAll = JJExceptionGuardUnrecognizedSelector | JJExceptionGuardDictionaryContainer | JJExceptionGuardArrayContainer | JJExceptionGuardZombie | JJExceptionGuardKVOCrash | JJExceptionGuardNSTimer | JJExceptionGuardNSNotificationCenter | JJExceptionGuardNSStringContainer,
};
```

* 设置异常类型并开启
```objc
    [JJException configExceptionCategory:JJExceptionGuardAll];
    [JJException startGuardException];
```

* 实时关闭保护
```objc
    [JJException stopGuardException];
```

* 当异常时，默认程序不会中断，如果需要遇到异常时退出，需要如下设置:
```objc
    //Default value:NO
    JJException.exceptionWhenTerminate = YES;
```

* Zombie使用黑名单机制，只有加入这个名单的才有作用,示例如下:
```objc
    [JJException addZombieObjectArray:@[TestZombie.class]];
```

* 如果需要记录日志，只需要实现`JJExceptionHandle`协议，并注册:
```objc
@interface ViewController ()<JJExceptionHandle>

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    [JJException registerExceptionHandle:self];
}

- (void)handleCrashException:(NSString*)exceptionMessage exceptionCategory:(JJExceptionGuardCategory)exceptionCategory extraInfo:(nullable NSDictionary*)info{

}
```

## 问题答疑

> 是否影响上线App Store

不会的，JJException的功能都是使用的官方API,没有任何私有API

> 保护App的实现技术原理是什么?

[JJException技术原理](https://github.com/jezzmemo/JJException/blob/master/JJExceptionPrinciple.md)

> JJException是否和Bugly和友盟等第三方库是否有冲突？

Bugly和友盟是记录Crash Bug的log还有一些统计功能，JJException主要是通过Hook技术来实现，所以不会和JJException冲突，__只有一种情况会有冲突，第三方库和JJException，Hook同样的方法，可能导致JJException不起作用__

> 如何上传异常信息到Bugly？

Bugly可以帮我们解决重复信息和CallStack信息，以及状态维护。  
实现JJExceptionHandle协议后，将异常信息组织成Error，然后用[Bugly reportError:error]上传异常信息，上传后异常信息Bugly的后台`错误分析`菜单里


## TODO(大家记得给我星哦)
* 增强Hook的健壮性
* 支持多平台（Mac OS X,TVOS,WatchOS）

## License
JJException is released under the MIT license. See LICENSE for details.
