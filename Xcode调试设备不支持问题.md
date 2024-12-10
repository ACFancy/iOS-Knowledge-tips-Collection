### Xcode: Could not locate device support files
- 解决方案
- Actually, there is a way. You just need to copy the DeviceSupport folder for iOS X.X (eg. iOS 13.5) from the older Xcode to the new one. It's located in:
```
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport/7.1
```

- If you don't have the iOS X.X (eg. iOS 13.5) files anymore, you can download a previous version of Xcode, extract it, and then copy these files to following path
```
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport/
```
- DeviceSupport download
- [Github Download page](https://github.com/JinjunHan/iOSDeviceSupport)
