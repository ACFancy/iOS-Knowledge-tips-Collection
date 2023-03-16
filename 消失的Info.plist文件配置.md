### 消失的Info.plit 文件配置
-  Xcode 13之后新建的项目，Xcode会自动配置在Build Settings中多了一个配置项 Packaging => Generate Info.plist File: YES, 和在Info配置中对应在Build Settings
   Info.plist Values加粗表示对应的设置了
-  针对 Generate Info.plist File: YES Xcode build之后，会自动把Info.plist 文件 对应的一些和Info的配置项删除掉，留下一些ATS等简单的配置
-  针对横竖屏，对于iPad 来说，一定要注意：在Info 下，配置 Application Scene Manifest => Enable Multiple Windows: NO 才能限制为在Info或者General 配置好的 横竖屏配置
   否则只会所有方向支持，注意 Supported Orientation(iPad)的配置
- 假如需要恢复之前老配置：
   - 关闭 Generate Info.plist File: NO
   - 补充完整的 Info.plist的文件配置
     - 可以从 Info下面 copy配置项 到Info.plist文件中来
### 以下是相关配图、
 - Info:
   ![image](https://user-images.githubusercontent.com/10044815/225556200-c6aea4aa-5a89-4b49-8a75-126f864e2914.png)
   ![image](https://user-images.githubusercontent.com/10044815/225556588-82790eb6-644c-4452-9730-1fffdd809eb2.png)
 - Generate Info.plist File配置：
   ![image](https://user-images.githubusercontent.com/10044815/225556434-0859b69f-921c-4ed1-9c1a-fde3389cd457.png)
 - General:
   ![image](https://user-images.githubusercontent.com/10044815/225556704-30313425-e286-4d32-8831-bd67db96658e.png)
