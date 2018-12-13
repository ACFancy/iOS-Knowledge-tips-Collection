## 符号表解析步骤
1. 查找 symbolicatecarsh 路径
```
find {YourComputerPath}/Xcode.app -name symbolicatecrash -type f
```
2. copy symbolicatecarsh 到你自己新建的文件夹下比如（{YourComputerPath}/{YourCreatedFloder}）
```
cp {YourComputerPath}/Xcode.app/Contents/Developer/Platforms/AppleTVSimulator.platform/Developer/Library/PrivateFrameworks/DVTFoundation.framework/symbolicatecrash {YourComputerPath}/{YourCreatedFloder}/symbolicatecrash
```
3. copy crash 文件和 dSYM符号表文件到 自己新建的文件夹下比如（{YourComputerPath}/{YourCreatedFloder}）
```
 cp xx/xxx.crash {YourComputerPath}/{YourCreatedFloder}/xxx.crash
 cp xx/xxx.dSYM  {YourComputerPath}/{YourCreatedFloder}/xxx.dSYM
```
4. 打开Terminal, cd 到自己新建的文件夹下比如（{YourComputerPath}/{YourCreatedFloder},执行以下指令操作
```
 cd {YourComputerPath}/{YourCreatedFloder}
 export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
```
5. 将crash完全符号化,先在Terminal, cd到自己新建的文件夹下比如（{YourComputerPath}/{YourCreatedFloder}
```
cd {YourComputerPath}/{YourCreatedFloder}
 ./symbolicatecarsh ./xxx.crash ./xxx.dSYM > xxx.log
```
6. dwarfdump 或者 atos 指令进行堆栈地址查询和查询对应地址执行的code的字符串，比如：
```
   dwarfdump --lookup 0x00159fc1  -arch arm7 xxx.dSYM
   atos -arch arm64 -o xxx.dSYM/Contents/Resources/DWARF/xxx -l 0x1000e4000 0x00000001000effdc
```
## Crash文章链接
1. [漫谈iOS Crash收集框架](https://nianxi.net/ios/ios-crash-reporter.html)
