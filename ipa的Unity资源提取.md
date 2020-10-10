### 提取工具AssetStudio（Windows系统）
- [工具链接](https://github.com/Perfare/AssetStudio/releases)
### 游戏安装包（APK或者IPA，IPA可通过越狱渠道下载或者iTunes应用搜索）
- APK提取步骤
  - 将APK的文件后缀修改为zip，直接解压缩，得到跟APK同名的文件夹
  - 在解压缩生成的文件夹中找到assets/bin/Data文件夹，使用AssetStudio打开
  - 直接导出游戏资源
- IPA提取步骤
  - 将IPA的文件后缀修改为zip，直接解压缩，得到Payload文件夹
  - 在Payload文件夹找到跟IPA同名的.app文件，右击此文件，点击“显示包内容”，找到Data文件夹，拷贝到其他目录，使用AssetStudio打开
  - 直接导出游戏资源
### 查看APK中的源码
- 将APK的文件后缀修改为zip，直接解压缩，得到跟APK同名的文件夹
- 解压缩生成的文件夹中找到assets/bin/Data/Managed/Assembly-CSharp.dll文件
- 使用Visual Studio打开（将dll直接拖到VS中），即可查看源码

### 参考：
- [简书链接](https://www.jianshu.com/p/bc2257332722)
