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

