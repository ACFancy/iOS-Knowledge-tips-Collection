## iOS 越狱

### iOS 11.3 越狱步骤
```
1. 搜索Electra官网，下载最新版本，使用vfs版本。（我使用的是1.1.0版本的vfs）
2. 搜索Impactor官网，下载最新版本
3. USB，iPhone连接Mac, 打开Impactor.app
4. 将vfs版本的ipa直接拖到 打开的Impactor软件，然后会弹出一个要输入苹果开发者账号的弹窗
5. 输入一个自己的苹果开发者项目后，就会开始安装
  5.1 这里会自动帮你将ipa的签名重新签名为你自己苹果开发者账号
  5.2 到设置，通用中信任下签证
6. 打开Electra, 点击jailbreak。可能会重启几次，然后再次打开会出现 Share，和安装好Cydia软件，表示越狱成功
  6.1 这里的jailbreak应该会有坑，像出现 Error:Exploit错误，不断重启的问题。
  6.2 出现上面的问题，如果在airplane Mode 下重试n次都不行的话，建议卸载Electra，多次重新安装Electra(可以使用不同版本的vfs的ipa)
  6.3 恭祝坚持到最后的会成功越狱成功。
```

### 安装Frida
```
iPhone安装Frida
1.官网链接：https://www.frida.re/docs/ios/
2.Start Cydia and add Frida’s repository by going to Manage -> Sources -> Edit -> Add
3. enter https://build.frida.re.
4. You should now be able to find and
   install the Frida package which lets Frida inject JavaScript into apps running on your iOS device
Mac安装Frida
1. 官网安装链接：https://www.frida.re/docs/installation/
2. 使用命令： pip install frida-tools
3. 安装成功后，打开Cydia软件（前提已安装Frida），USB连接到Mac
4. 使用命令： frida-ps -U 验证是否安装成功
```

### 安装Cycript
```
 1.官网地址：http://www.cycript.org/ 下载 SDK
 2. 将SDK 加压到 /opt 目录下
```

### Mac上调用cycript出错：dyld: Library not loaded
```
在Mac使用cycript在非越狱手机上调试时，调用：./cycript报错：
1.错误原因：是因为电脑的ruby版本不匹配
2.cd /System/Library/Frameworks/Ruby.framework/Versions/ 查看当前ruby版本
3.关闭系统的SIP
3.1 电脑重启按住command+R，进入恢复模式
3.2 打开终端，输入csrutil disable，重启
3.3 如果想打开SIP，重复上两步，命令改为csrutil enable
4.直接把2.3的复制一份，改为2.0即可，运行如下命令：
  $ sudo mkdir -p /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/
  $ sudo ln -s /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/libruby.2.3.0.dylib  /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/libruby.2.0.0.dylib
5.现在调用./cycript能正常进入cy模式了：
```

### 安装theos
```
1. sudo git clone --recursive https://github.com/theos/theos.git /opt/theos
2. export THEOS=/opt/theos 在文件 ~/.profile 中添加
3. 可以用这个命令行工具 source ~/.zshrc（可选）
4. https://github.com/theos/sdks 下载 theos sdk拷贝到 /opt/theos/sdks 目录下
5. brew install ldid xz
6. curl https://ghostbin.com/ghost.sh -o 找一个可以写入的文件（自定义为ghost）
7. 拷贝上面的文件 到 opt/theos/bin 目录下
8. chmod +x opt/theos/bin/ghost
9. 详细见： https://github.com/theos/theos/wiki/Installation-macOS 和 https://github.com/AloneMonkey/MonkeyDev/issues/18
```

### 安装MonKeyDev
```
1.brew install ldid
2.通过以下命令选择指定的Xcode进行安装
2.1 sudo xcode-select -s /Applications/Xcode.app
2.2 xcode-select -p
3.执行安装命令
3.1 sudo /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/AloneMonkey/MonkeyDev/master/bin/md-install)"
4.执行卸载
4.1 sudo /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/AloneMonkey/MonkeyDev/master/bin/md-uninstall)"
5.更新
5.1 sudo /bin/sh -c "$(curl -fsSL https://raw.githubusercontent.com/AloneMonkey/MonkeyDev/master/bin/md-update)"
6.执行安装完成后，会在目录 /opt 下多一个 MonkeyDev的目录
7.打开对应的XCode,新建项目滑到最低不会出现，新建MonkeyDev的选项
8.详细见： https://github.com/AloneMonkey/MonkeyDev/wiki
9.MonkeyDev 默认集成是最新版本，需要把自己的 RevealServer.framework放到/opt/MonkeyDev/frameworks下（打开 Reveal，点击 reveal - help - show reveal in finder 即可找到 RevealServer.framework），这样就可以查看时图层级。
```
### ipa重签名工具 ios-app-signer
 ```
 1. https://dantheman827.github.io/ios-app-signer/
 2. 下载解压，将ipa拖入，然后使用你自己的开发者账号
 3. 然后点击start 等待重签名完成即可
 ```
