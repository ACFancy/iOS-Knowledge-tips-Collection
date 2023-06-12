### Mac Terminal.app 打开就闪退，其他用户正常使用的问题
- 解决方案
   ```shell
   在访达中把下面的文件夹删除：
  ~/Library/Saved Application State/com.apple.Terminal.savedState 
    或者直接运行命令：
    rm -fr ~/Library/Saved\ Application\ State/com.apple.Terminal.savedState
   ```
