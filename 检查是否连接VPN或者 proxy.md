#### iOS 9.0之前
```objc
#include <ifaddrs.h>

- (BOOL)isVPNConnected {
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;

    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while (temp_addr != NULL) {
            NSString *string = [NSString stringWithFormat:@"%s" , temp_addr->ifa_name];
            if ([string rangeOfString:@"tap"].location != NSNotFound ||
                [string rangeOfString:@"tun"].location != NSNotFound ||
                [string rangeOfString:@"ppp"].location != NSNotFound){
                return YES;
            }
            temp_addr = temp_addr->ifa_next;
        }
    }

    // Free memory
    freeifaddrs(interfaces);
    return NO;
}

+ (BOOL)getProxyStatus {
    NSDictionary *proxySettings = NSMakeCollectable([(NSDictionary *)CFNetworkCopySystemProxySettings() autorelease]);
    NSArray *proxies = NSMakeCollectable([(NSArray *)CFNetworkCopyProxiesForURL((CFURLRef)[NSURL URLWithString:@"http://www.google.com"], (CFDictionaryRef)proxySettings) autorelease]);
    NSDictionary *settings = [proxies objectAtIndex:0];
    
    NSLog(@"host=%@", [settings objectForKey:(NSString *)kCFProxyHostNameKey]);
    NSLog(@"port=%@", [settings objectForKey:(NSString *)kCFProxyPortNumberKey]);
    NSLog(@"type=%@", [settings objectForKey:(NSString *)kCFProxyTypeKey]);
    
    if ([[settings objectForKey:(NSString *)kCFProxyTypeKey] isEqualToString:@"kCFProxyTypeNone"])
    {
        //没有设置代理
        return NO;
    }
    else
    {
        //设置代理了
        return YES;
    }
}
```
### iOS 9.0之后
```objc
- (BOOL)isVPNConnected
{
    NSDictionary *dict = CFBridgingRelease(CFNetworkCopySystemProxySettings());
        NSArray *keys = [dict[@"__SCOPED__"]allKeys];
        for (NSString *key in keys) {
            if ([key rangeOfString:@"tap"].location != NSNotFound ||
                [key rangeOfString:@"tun"].location != NSNotFound ||
                [key rangeOfString:@"ppp"].location != NSNotFound){
                return YES;
            }
        }
        return NO;
}
```
```swift
func isVPNConnected() -> Bool {
    let cfDict = CFNetworkCopySystemProxySettings()
    let nsDict = cfDict!.takeRetainedValue() as NSDictionary
    let keys = nsDict["__SCOPED__"] as! NSDictionary

    for key: String in keys.allKeys as! [String] {
        if (key == "tap" || key == "tun" || key == "ppp" || key == "ipsec" || key == "ipsec0") {
            return true
        }
    }
    return false
}

func isConnectedToVpn() -> Bool {
    let host = "www.example.com"
    guard let reachability = SCNetworkReachabilityCreateWithName(nil, host) else {
        return false
    }
    var flags = SCNetworkReachabilityFlags()
    if SCNetworkReachabilityGetFlags(reachability, &flags) == false {
        return false
    }
    let isOnline = flags.contains(.reachable) && !flags.contains(.connectionRequired)
    if !isOnline {
        return false
    }
    let isMobileNetwork = flags.contains(.isWWAN)
    let isTransientConnection = flags.contains(.transientConnection)
    if isMobileNetwork {
        if let settings = CFNetworkCopySystemProxySettings()?.takeRetainedValue() as? Dictionary<String, Any>,
           let scopes = settings["__SCOPED__"] as? [String:Any] {
            for (key, _) in scopes {
                if key.contains("tap") || key.contains("tun") || key.contains("ppp") {
                    return true
                }
            }
        }
        return false
    } else {
        return isTransientConnection
    }
}

func isConnectedToProxy() -> Bool {
    let host = "http://www.example.com"
    if let url = URL(string: host),
       let proxySettingsUnmanaged = CFNetworkCopySystemProxySettings() {
        let proxySettings = proxySettingsUnmanaged.takeRetainedValue()
        let proxyUnmanaged = CFNetworkCopyProxiesForURL(url as CFURL, proxySettings)
        if let proxies = proxyUnmanaged.takeRetainedValue() as? [[String : AnyObject]], proxies.count > 0 {
            let proxy = proxies[0]
            let key = kCFProxyTypeKey as String
            let value = kCFProxyTypeNone as String
            if let v = proxy[key] as? String, v != value {
                return true
            }
        }
    }
    return false
}
```



### 参考
- [博客](https://xdev.in/posts/check-vpn-connectivity/)
