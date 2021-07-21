### Xcode build Release配置 提示bitcode 问题
-  All object files and libraries for bitcode must be generated from Xcode Archive or Install build file
- 解决方案
  ```
  On Build Settings -> Other C flags, 
  set Debug to -fembed-bitcode-marker,
  and Release to -fembed-bitcode. Make sure that is set for your project, and not the targets.
  ```
- 参考链接
  - [原址](https://medium.com/@heitorburger/static-libraries-frameworks-and-bitcode-6d8f784478a9)
  - [stackoverflow](https://stackoverflow.com/questions/36480665/xcode-project-build-successfully-but-when-archiving-bitcode-bundle-could)
