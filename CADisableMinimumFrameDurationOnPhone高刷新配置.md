## CADisableMinimumFrameDurationOnPhone
- 针对iPhone 13或者 iPhone 13Pro以上的机型
 ```info.plist
 <key>CADisableMinimumFrameDurationOnPhone</key><true/>
 ```
 
- 通过在 CADisplayLink 回调中确认 duration 参数，计算得到当前屏幕的实时刷新率，并修改 preferredFrameRateRange 来进行跟踪。
- 优点：
  - 方案相对简单，只需在每次回调中更新 DisplayLink 对象的 preferredFrameRateRange 属性即可
- 缺点：
    - 由于动态帧率的存在，FPS 指标可以反映实时屏幕刷新情况，但是聚合后的意义不大，消费时需要区分特定机型/场景
    - 观察到目前的最小回调频率为 60Hz，也就是说无法确认 ProMotion 屏幕在 48Hz、30Hz 甚至更低刷新率下的表现
    - 在低刷新率时，MainRunLoop 依然会以 60Hz 运行，对功耗有一定影响

- 需要注意的是，CADisplayLink 的 preferredFrameRateRange 需要以类似一下格式进行设置：
 ```objc
 NSInteger currentFPS = (NSInteger)ceil(1.0 / displayLink.duration);
 displayLink.preferredFrameRateRange = CAFrameRateRangeMake(10.0, currentFPS, 0.0);
 /// CAFrameRateRange.minimum 传最小值 10.0，preferred 传 0.0，可以让该 CADisplayLink 只用于监控当前的系统帧率，而不影响帧率的动态选择。
 CADisplayLink *dp = ...
dp.paused = YES;
[dp addToRunLoop:[NSRunLoop mainRunLoop] forMode:NSRunLoopCommonModes];

CFRunLoopAddObserver(CFRunLoopGetMain(),
                         CFRunLoopObserverCreateWithHandler(kCFAllocatorDefault, kCFRunLoopEntry | kCFRunLoopExit, YES, 0,
                                                            ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) {
        if (activity == kCFRunLoopEntry) {
             dp.paused = NO;
             dp.preferredFramePerSecond = 120;
        } else {
             dp.paused = YES;
             dp.preferredFramePerSecond = 0;
        }
    }), (__bridge CFStringRef)UITrackingRunLoopMode);
 ```

### 参考
- [apple](https://developer.apple.com/documentation/quartzcore/optimizing_promotion_refresh_rates_for_iphone_13_pro_and_ipad_pro)
- [掘金](https://juejin.cn/post/7074759817738321956#heading-30)
