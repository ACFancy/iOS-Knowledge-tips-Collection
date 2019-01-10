##iOS 越狱

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
