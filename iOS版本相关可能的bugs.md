### iOS13三指撤销
- 解决方案
 - 将文案判长和截取异步添加到主队列，在下一个runloop执行。
    ```Objective-C
     - (void)textChange:(UITextField *)textField {
         dispatch_async(dispatch_get_main_queue(), ^{
         ... ...
         });
     }
    ```
 - 数字截断后 crash
    - 数字输入限制长度后，超过长度后继续输入，这个时候撤销也会产生crash，而且上面的方法不可行。目前想到的方案是在UITextField的回调方法进行输入的拦截。
    ```Objective-C
      - (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {
           /// 输入数字后截取字符串仍旧可以触发撤销操作导致crash, 在这里拦截一下
             if (textField.keyboardType == UIKeyboardTypeNumberPad
                && range.location >= textField.tt_maxLength) {
                  return NO;
             }
            return YES;
      }
    ```  
- 参考 [!链接](https://juejin.im/post/6844903952526344205)

### iOS 16.0发布后上传到App Store后台包会crash的问题
- 提示libswiftCoreData or libswiftCoreGraphics not loaded的问题
- 解决方案
```
We've been looking into this issue, and it took some time to understand the different scenarios presented by the crashes you are reporting involving CoreGraphics, Foundation, and CoreData. Thank you to those who filed bug reports in Feedback Assistant, provided full crash reports here on the forums, and filed Technical Support Incidents.

There is a single underlying issue which affects the Swift runtime support for OS versions prior to the addition of the Swift runtime as a library provided by the operating system This issue is then manifested as a crash through 2 different paths.

If you are building your app with any version of Xcode 13 and submit these builds to the App Store, your app downloaded from the App Store will crash with a missing library error when run on iOS 9 - 12.1. You can avoid this crash by disabling bitcode in your app's build settings, rebuilding your app, and submitting an update to the App Store. In general, you are able to build and test your app on these iOS versions directly from Xcode without being affected.

If you are building your app with Xcode 14 or Xcode 14.0.1, your app will crash on older OS versions, including while building and testing directly from Xcode, as well as after submitting the app to the App Store. The OS versions affected include iOS 11 - 12.1, macOS 10.13 - 10.14.3, as well as watchOS and tvOS versions from the same time frame. To work around the crash when building with Xcode 14, add -Wl,-weak-lswiftCoreGraphics (substituting other library names for swiftCoreGraphics if appropriate for your crash) to the Other Linker Flags build setting.

To verify the workaround for the second scenario, inspect the binary with otool -L /Path/To/Binary before and after applying this setting. Before applying this setting, you will see the Swift library referenced at /usr/lib/swift/libswiftCoreGraphics.dylib (again, substituting the appropriate library name given your crash report), and after applying this workaround, you should see that change to @rpath/libswiftCoreGraphics.dylib instead, which is what you want to see.
```
- 参考链接 [链接](https://developer.apple.com/forums/thread/714795?page=2)
