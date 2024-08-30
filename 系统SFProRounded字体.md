### 系统SF Pro Rounded字体
- 在内部系统命名为 .AppleSystemUIFontRounded
- SwiftUI
  ```swift
            Text("XX")
            .font(.system(size: 12, weight: .semibold, design: .rounded))

  ```
- UIKit
  ```swift
      class func rounded(ofSize size: CGFloat, weight: UIFont.Weight) -> UIFont {
        let systemFont = UIFont.systemFont(ofSize: size, weight: weight)
        let font: UIFont

        if let descriptor = systemFont.fontDescriptor.withDesign(.rounded) {
            font = UIFont(descriptor: descriptor, size: size)
        } else {
            font = systemFont
        }
        return font
    }
  ```
  - CoreText
  ```swift
        if let font = CTFontCreateWithName(".AppleSystemUIFontRounded" as CFString, 24, nil) as UIFont? {
            self.font = font
        }

        if let font = CTFontCreateWithName(".AppleSystemUIFontRounded-Semibold" as CFString, 24, nil) as UIFont? {
            self.font = font
        }
  ```
