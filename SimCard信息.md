### 获取Sim card信息，新api标明12.0开始，实际从12.1开始
- oc 代码如下
  ```objc
  #import <CoreTelephony/CTTelephonyNetworkInfo.h>
  #import <CoreTelephony/CTCarrier.h>
  
   + (NSDictonary *)getSimCardInfo {
      CTTelephonyNetworkInfo *info = [CTTelephonyNetworkInfo new];
      CTCarrier *carrier = nil;
      NSString *radioType = nil;
      if (@available(iOS 12.1, *)) {
        if (info && [info respondsToSelector: @selector(serviceSubscriberCellularProviders)]) {
          NSDictionary *dict = [info serviceSubscriberCellularProviders];
          if (dict && dict.allKeys.count) {
              carrier = [dict objectForKey: dict.allKeys[0]];
          }
        }
        
        if (info && [info respondsToSelector: @selector(serviceCurrentRadioAccessTechnology)]) {
          NSDictionary *dict = [info serviceCurrentRadioAccessTechnology];
          if (dict && dict.allKeys.count) {
              radioType = [dict objectForKey: dict.allKeys[0]];
          }
        }
      } else {
        carrier = [info subscriberCellularProvider];
        radioType = [info currentRadioAccessTechnology];
      }
      
      // 运营商是否可用
      BOOL use = carrier.allowsVOIP;
      // 运营商名字
      NSString *name = carrier.carrierName;
      // ISO国家代码
      NSString *code = carrier.isoCountryCode;
      // 移动国家代码
      NSString *mcc = [carrier mobileCountryCode];
      // 移动网络代码
      NSString *mnc = [carrier mobileNetworkCode];
      return @{@"AllowsVOIP": @(use), 
               @"carrierName": name ?: @"NONE",
               @"isoCountryCode": code?: @"NONE",
               @"mobildCountryCode": mcc?: @"NONE",
               @"mobileNetworkCode": mnc?: @"NONE",
               @"radioAccessTechnology": radioType?: @"NONE"}
   }
  
  ```
