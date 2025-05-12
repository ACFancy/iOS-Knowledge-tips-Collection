### iOS 18适配
#### UITabBarController 适配
> 主要是iPad OS的适配，保持原来的UITabBar的样式，不使用iOS 18新增的UISiderBar
- 解决方案(自定义UITabBarController类，override下面的属性)
 - 1. 不推荐的方案，编译器会警告
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
 - 2. 进针对元原生的tabBar
  ```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        if #available(iOS 18, *), Device.current.isPad {
            // @NOTE 适配iPad OS 18之后保留原来的UITabBar的UI
            self.traitOverrides.horizontalSizeClass = .compact
        }
        self.setValue(self.mainTabBar, forKey: "tabBar")
        // other code
      }
  ```
 - 3. 针对需要覆盖原生的tabBar
   ```swift
       override func viewDidLoad() {
        super.viewDidLoad()
        if #available(iOS 18, *), Device.current.isPad {
            // @NOTE 适配iPad OS 18之后保留原来的UITabBar的UI
            self.traitOverrides.horizontalSizeClass = .compact
            self.view.insertSubview(self.mainTabBar, aboveSubview: self.tabBar)
            self.tabBar.removeFromSuperview()
            self.tabBar.isHidden = true
        }
        self.setValue(self.mainTabBar, forKey: "tabBar")
        // other code
      }
   ```
