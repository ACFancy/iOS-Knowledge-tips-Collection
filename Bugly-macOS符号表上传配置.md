### Bugly-macOS配置
- 本地环境配置  
  - iOS SDK版本: 2.5.0 [Bugly-iOS SDK](https://github.com/BuglyDevTeam/Bugly-iOS)
  - 对应 bugly 上传工具 版本 3.3.4 [bugly上传工具](https://bugly.qq.com/v2/sdkDownload?id=d796e9d7-0423-422f-9eb9-63b6e16ef4f9)
  - 当前对应的jdk版本 1.8.0.201 下载并安装 [1.8下载](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html)
  - 本地macOS下执行下面脚本
    ```sh
    brew install jenv
    # bash 则执行下述命令
    echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(jenv init -)"' >> ~/.bash_profile
    source ~/.bash_profile
    
    # zsh 则执行下述命令
    echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
    echo 'eval "$(jenv init -)"' >> ~/.zshrc
    source ~/.zshrc
    
    /usr/libexec/java_home -V
    # Matching Java Virtual Machines (3):
    # 15 (x86_64) "Oracle Corporation" - "Java SE 15" /Library/Java/JavaVirtualMachines/jdk-15.jdk/Contents/Home
    # 1.8.201.09 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    # 1.8.0_201 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
    # /Library/Java/JavaVirtualMachines/jdk-15.jdk/Contents/Home
    # 添加对应路径比如:
    jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
    jenv versions
      # system
      # 1.8
      # * 1.8.0.201 (set by JENV_VERSION environment variable)
      # oracle64-1.8.0.201 
      # JEnv 的精髓是 shell、local 和 global 三个参数命令。shell 用于设置终端窗口生命周期内使用的 JDK 版本；local 用于设置当前目录下使用的 JDK 版本；而 global 用于设置全局使用的 JDK 版本。这三个命令的使用方式都一样：
     jenv shell/local/global 1.8.0.201
     java -version
      # java version "1.8.0_201"
      # Java(TM) SE Runtime Environment (build 1.8.0_201-b09)
      # Java HotSpot(TM) 64-Bit Server VM (build 25.201-b09, mixed mode)
     # iOS端执行下面shell脚本
     java -jar buglyqq-upload-symbol.jar -appid "<Your Bugly appid>" \
                                    -appkey "<Your Bugly appkey>" \
                                    -bundleid "<Your app bundle id>" \
                                    -version "<Your bugly custom app version>" \
                                    -platform "IOS" \
                                    -inputSymbol "<Your dSym files dir path>"
    ```
  - 可能碰到的问题
    -  jar文件不能打开
    -  jar文件不能访问
  - 解决方案
    - 检查对应的jdk版本是不是正确的
    - 需要放到别的目录下去执行，当前目录没有权限执行
  - 执行成功后会有下面文件自动生成
    - buglybin文件夹
    - buglyqq-upload-symbol.jar(一开始解压上传符号表工具就有)
    - cp_buglyQqUploadSymbolLib.jar
    - cp_buglySymboliOS.jar
