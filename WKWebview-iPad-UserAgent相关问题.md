#### WKWebview iPad UserAgent相关问题
- iOS 13以上的比如ipad mini6 H5显示的不是Mobile的样式，（比如百度的居然加载不出来，一直循环调用didFinsh回调的代理函数）
   ```ruby
   iPad iOS13.5，  
  Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.1.1 Safari/605.1.15  

  iPad iOS12.4.6，  
  Mozilla/5.0 (iPad; CPU OS 12_4_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.1.2 Mobile/15E148 Safari/604.1 

  iPad Mini iOS13.1.3
  Mozilla/5.0 (iPad; CPU iPhone OS 13_1_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.1 Mobile/15E148 Safari/604.1
   ```
- 原因
   - WWDC2019苹果发布了iOS13和iPad OS，从此iPad有了自己的操作系统。
   - 随着iPad的尺寸越来越大，苹果为了更好的用户体验，一屏幕内可以展示更多的内容，在iOS13中，苹果改进Safari，新增了一项功能——“请求桌面网站”。
   - 通俗地讲，就是为了让iPad用起来像PC。这个功能还“默认”开启，通过iPad设备打开Safari进入网站时会访问PC网站，而不是移动端网站。userAgent值不就完了吗？假装自己是PC
- 如何在Safari上禁用这个功能？
   - 设置-Safari-请求桌面网站
   - iOS13的iPhone上也有这个开关，只是默认是关闭的
   - Safari上的这个开关只影响Safari，不影响WKWebView。即使你关闭了这个开关，App内部创建的WKWebView仍然是默认值。
- WKWebview解决方案
   - iOS13，WKWebView新增了几个和“请求桌面网站”相关的API
    ```objc
    @interface WKWebViewConfiguration : NSObject <NSSecureCoding, NSCopying>

    @property (null_resettable, nonatomic, copy) WKWebpagePreferences *defaultWebpagePreferences API_AVAILABLE(macos(10.15), ios(13.0));

    @end
    typedef NS_ENUM(NSInteger, WKContentMode) {
        WKContentModeRecommended,
        WKContentModeMobile,
        WKContentModeDesktop
    } API_AVAILABLE(ios(13.0));

    WK_EXTERN API_AVAILABLE(macos(10.15), ios(13.0))
    @interface WKWebpagePreferences : NSObject
    @property (nonatomic) WKContentMode preferredContentMode API_AVAILABLE(ios(13.0));
    @end
    /***
     preferredContentMode属性的默认值是WKContentModeRecommended，而WKContentModeRecommended会根据设备不同分别进入移动网站和PC网站。
     iPhone和iPad mini设备默认访问移动网站，iPad(iPad mini6)、Mac设备默认访问PC网站。
    */ 
    @protocol WKNavigationDelegate <NSObject>
    @optional

    //以前的方法
    - (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler;

    //新增的方法
    - (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction preferences:(WKWebpagePreferences *)preferences decisionHandler:(void (^)(WKNavigationActionPolicy, WKWebpagePreferences *))decisionHandler API_AVAILABLE(macos(10.15), ios(13.0));

    @end
    ```
    - App方案（苹果推荐方案，不过这种设置不一定起作用(比如百度首页<2021.11.12>)，需要前端页进行配合）
       ```objc
        WKWebViewConfiguration *configuration = [[WKWebViewConfiguration alloc] init];
        if (@available(iOS 13.0, *)) {
           configuration.defaultWebpagePreferences.preferredContentMode = WKContentModeMobile;
        }
       ```
     - 前端方案(不是最佳方案，临时解决方案)
         ```js
         /*
          * ipad环境判断更新
          * iOS pre 13 以前以ua作判断，13后以platform及maxTouchPoints做判断
          */
          isiPad = (navigator.userAgent.match(/(iPad)/) || (navigator.platform === 'MacIntel' && navigator.maxTouchPoints > 1))
         ```
- 参考链接
   - [iOS13 苹果“偷摸地“改了iPad的userAgent，你被坑过吗](https://juejin.cn/post/6865658712665620494#heading-8)
