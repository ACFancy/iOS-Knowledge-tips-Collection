#### cocoapods-imy-bin
 - [github主页Repo](https://github.com/MeetYouDevs/cocoapods-imy-bin)
 - [使用教程](https://github.com/MeetYouDevs/cocoapods-imy-bin/blob/master/%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)
#### 环境搭建的坑
 - MongoDB 数据保存的路径创建的问题
   ```shell
   mkdir ~/data 
   cd ~/data
   pwd
   <data文件夹路径>
   sudo mongod --dbpath=<data文件夹路径>
   ```
 - binary-Server启动
   - 需要下载该项目[Binary-Server](https://github.com/su350380433/binary-server)或者Demo[Demo/BinaryServer](https://github.com/su350380433/cocoapods-imy-bin-demo)
#### 制作二进制的坑
  - 需要在项目下有 podspec的项目同名的podspec文件（格式参考Demo)
  - 需要在项目下有一个白名单(参考项目Demo的BinArchive.json)
  - 需要把本项目添加到白名单中(比如Demo)
  - 使用这个命名进行制作和上传二进制(首先按照教程配置好mongoDB和binaryServer并启动
    ```shell
      pod bin init 配置一些自定义的操作
      pod bin auto --all-make
    ```
