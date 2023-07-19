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
let configuration = WKWebViewConfiguration()
configuration.setURLSchemeHandler(handler, forURLScheme: "customer-scheme")
let wkWebView = WKWebView(frame: .zero, configuration: configuration)
```

