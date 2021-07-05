### 模拟内存警告
```swift
 NotificationCenter.default.post(name: UIApplication.didReceiveMemoryWarningNotification, object: nil)
```
### 模拟kill app
```swift
let sel = NSSelectorFromString("terminateWithSuccess")
if UIApplication.shared.responds(to: sel) {
    UIApplication.shared.perform(sel)
}
```
