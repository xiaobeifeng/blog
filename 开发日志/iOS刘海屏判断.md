根据安全区的概念来判断是否是刘海屏

iPhoneX 的出厂系统都是 iOS11 及以上

可以利用 `CGFloat a = [[UIApplication sharedApplication] delegate].window.safeAreaInsets.bottom；`

当 a 大于 0 时即是 iPhone X ，XR，XS ，XS Max 等。

```objc

if (@available(iOS 11.0, *)) {
        CGFloat a =  [[UIApplication sharedApplication] delegate].window.safeAreaInsets.bottom;
        NSLog(@"%f",a);
    } else {
        // Fallback on earlier versions
    }
```

PS: 宏定义这两个写法没亲测过

```objc
#define IPHONE_X \
({BOOL isPhoneX = NO;\
if (@available(iOS 11.0, *)) {\
isPhoneX = [[UIApplication sharedApplication] delegate].window.safeAreaInsets.bottom > 0.0;\
}\
(isPhoneX);})
```

```objc
#define isIphoneX ({\
BOOL isPhoneX = NO;\
if (@available(iOS 11.0, *)) {\
    if (!UIEdgeInsetsEqualToEdgeInsets([UIApplication sharedApplication].delegate.window.safeAreaInsets, UIEdgeInsetsZero)) {\
    isPhoneX = YES;\
    }\
}\
isPhoneX;\
})
```
