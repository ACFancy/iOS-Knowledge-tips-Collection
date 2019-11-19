#### iOS版本和arm架构支持
 - 最低版本设置为iOS 11.0之后，不再支持armv7 或 armv7s架构,在项目Build Setttings中的 valid Architectures中可以将 armv7和armv7s去除
 - armv7和armv7s去除上传到App Store后就不会再有 Too many symbol files的警告
 - 如果引入的第三方库带有armv7架构，可以参考使用（但可以先尝试上面的方法是否可以让第三方库不打包包含armv7的架构）
 ```
 post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      config.build_settings['VALID_ARCHS'] = 'arm64'
    end
  end
end
 ```
