# App Installation failed, No code signature found.
 - Solution:
  1. GO To "/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk"
  2. Open file SDKSettings.plist and check value for CODE_SIGNING_REQUIRED. If it is set 'NO' then set it 'YES'.
  3. Now relaunch the XCode. If it does not work again, then clear XCode DerivedData folder.
# 查找block中使用的self
 [参考](https://github.com/iteatimeteam/Friday-QA/issues/15)
 ```
 \^(\s*\(.*\)\s*)?\{(.|\n)*?\bself\b[^}]*\}
 ```
# 出包问题
 - Apps built with Xcode 13 or Xcode 13.1 that make use of Swift Concurrency features (such as async/await), deploy to iOS prior to 15, tvOS prior to 15, or watchOS prior to 8, and have bitcode enabled may crash at launch with an error reporting that the libswift_Concurrency.dylib library was not loaded
#### 解决方案
 - Workaround: Add -Wl,-weak-lswift_Concurrency -Wl,-rpath,/usr/lib/swift to Other Linker Flags in the app’s build settings.
 - use Xcode 13.1 with bitcode disabled for archives (not good because then app store can't recompile on the server to support new toolchains etc)
 - use Xcode 13.2 RC as normal (however, will then fail for iOS 12 devices with chkstk_darwin symbol issue)
 - add libswift_Concurrency.tbd in the "Link Binary with Libraries" build phase (and make it optional). (seems to be the most ideal solution)
#### 引入的原因
 - Xcode 13.2-13.0 2021.12.16号左右打包上传到testflight，app thinning分发到iOS 11,12的系统中app启动crash，上报的是libSwift-Concurrency.dylib not loaded的错误
 - 引入的原因是，在工程代码或者引入的库中使用了async await的Concurrency并发的新的api导致，不引入则不会出问题
#### 参考解决的方案
 - [解决方案](https://stackoverflow.com/a/70356479)
