###  拦截HTTPStatusCode 
####  1.处理方式如下：
```Objective-C
 (void)webView:(WKWebView *)webView 
 decidePolicyForNavigationResponse:(WKNavigationResponse *)navigationResponse
 decisionHandler:(void (^)(WKNavigationResponsePolicy))decisionHandler 
 {
    if (((NSHTTPURLResponse *)navigationResponse.response).statusCode == 200) {
        decisionHandler (WKNavigationResponsePolicyAllow);
    }else {
        decisionHandler(WKNavigationResponsePolicyCancel);
    }
}
```
### 自定义scheme拦截(iOS 11.0以上)
```swift
class LocalURLSchemeHandler: NSObject, WKURLSchemeHandler {
    func webView(_ webView: WKWebView, start urlSchemeTask: WKURLSchemeTask) {
        let request = urlSchemeTask.request
        guard let url = request.url else {
            return
        }
        switch url.scheme {
        case "customer-scheme":
        default: break
        }
    }
    func webView(_ webView: WKWebView, stop urlSchemeTask: WKURLSchemeTask) {
        debugPrint("DD - stop = \(urlSchemeTask)")
        urlSchemeTask.didFinish()
    } 
}
// @note 一定要在初始化webview之前对 WKWebViewConfiguration进行设置，否则不会生效
let configuration = WKWebViewConfiguration()
configuration.setURLSchemeHandler(handler, forURLScheme: "customer-scheme")
let wkWebView = WKWebView(frame: .zero, configuration: configuration)
```
- 参考链接
  - [掘金](https://juejin.cn/post/7219490209716158523#heading-10)
  - [掘金](https://juejin.cn/post/6844903828823736327#heading-9)
  - [掘金](https://juejin.cn/post/6997294531498475556/#heading-0)
  - [掘金](https://juejin.cn/post/6985769922143977480)
  - [掘金](https://juejin.cn/post/6997287680270663694)
