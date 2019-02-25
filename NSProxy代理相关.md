## 继承 NSProxy 的HWProxy 实现多个代理的实现
### HWProxy.h
```objc

#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

@interface HWProxy : NSProxy

@property (nonatomic, strong, readonly) NSPointerArray *objects;

- (instancetype)initWithObjects:(id)obj, ...;

@end

NS_ASSUME_NONNULL_END
```
### HWProxy.m
```objc
#import "HWProxy.h"

@implementation HWProxy

- (instancetype)initWithObjects:(id)obj, ...
{
    if (obj == nil) {
        @throw [NSException exceptionWithName:@"HWProxyErrorDomain" reason:@"Create HWProxy deleiver nil param" userInfo:nil];
    }
    NSPointerArray *pointArray = [NSPointerArray weakObjectsPointerArray];
    va_list args;
    [pointArray addPointer:(__bridge void * _Nullable)(obj)];
    va_start(args, obj);
    id arg;
    while ((arg = va_arg(args, id))) {
        [pointArray addPointer:(__bridge void * _Nullable)(obj)];
    }
    va_end(args);
    _objects = pointArray;
    return self;
}

#pragma mark - Override Methods
- (BOOL)respondsToSelector:(SEL)aSelector
{
    for (id obj in _objects) {
        if ([obj respondsToSelector:aSelector]) {
            return YES;
        }
    }
    return NO;
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel
{
    NSMethodSignature *sign;
    for (id obj in _objects) {
        sign = [obj methodSignatureForSelector:sel];
        if (sign) {
            return sign;
        }
    }
    
    return sign;
}

- (void)forwardInvocation:(NSInvocation *)invocation
{
    for (id obj in _objects) {
        if ([obj respondsToSelector:invocation.selector]) {
            [invocation invokeWithTarget:obj];
        }
    }
}

@end
```
