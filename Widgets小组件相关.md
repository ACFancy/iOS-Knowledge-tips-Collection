### Widgets小组件 Intents本地化
- 注意事项，对于添加的Intents文件的本地化，Xcode 14.x 为例，一定需要 project 使用base, 不然本地化会出问题
  - ![image](https://github.com/ACFancy/iOS-Knowledge-tips-Collection/assets/10044815/342ca29a-7005-46e1-9da6-4f30cbc8d0dd)
  - ![image](https://github.com/ACFancy/iOS-Knowledge-tips-Collection/assets/10044815/ad68d001-34f8-484c-8e75-f2b38ea2eda6)

### Widgets小组件适配问题
- Xcode 16.4之后打包的小组件, iOS 17.x以上添加到主屏幕后显示 Widget needs to adopt container background
   - containerBackground 是iOS 17之后新增的接口，用于统一设置View背景颜色。如果你的小组件使用了 background 设置背景颜色，可能需要适配新的 containerBackground API
- 适配方案
  - 单纯的背景适配
    ```swift
    View+Extension.swift
     @ViewBuilder
    func widgetBackground(_ bgView: some View) -> some View {
        if #available(iOS 17, *) {
            containerBackground(for: .widget) {
                bgView
            }
            .background(bgView)
        } else {
            background(bgView)
        }
    }
    ```
   - 需要适配下间距问题，直接吧safeInsets忽略掉
      ```swift
        extension WidgetConfiguration {
            func mlt_contentMarginsDisabled() -> some WidgetConfiguration {
                if #available(iOS 15, *) {
                    return self.contentMarginsDisabled()
                } else {
                    return self
                }
            }
        }
      ```
