### iOS 18适配
#### UITabBarController 适配
> 主要是iPad OS的适配，保持原来的UITabBar的样式，不使用iOS 18新增的UISiderBar
- 解决方案(自定义UITabBarController类，override下面的属性)
  ```swift
  override var traitCollection: UITraitCollection {
      let value = super.traitCollection
      if #available(iOS 18, *), UIDevice.current.userInterfaceIdiom == .pad {
          return value.modifyingTraits { mutableTraits in
              mutableTraits.userInterfaceIdiom = .phone
          }
      }
      return value
  }
  ```
