### 在 iOS 15.0以下 SwiftUI View的 .sheet 和 .fullScreenOver在同一个视图上同时调用的问题
- 解决方案，放在不同的视图上
- 参考: [SOLVED: Using .sheet and .fullScreenCover together?](https://www.hackingwithswift.com/forums/swiftui/using-sheet-and-fullscreencover-together/4258)
```swift
 ZStack {

                // Allow Sheet and FullScreenCover to work together
                EmptyView()
                    .sheet(isPresented: self.matches(mode: .present), onDismiss: {
                        self.navigator.pop()
                    }) {
                        presentView()
                    }

                switchView()
                    .fullScreenCover(isPresented: self.matches(mode: .fullscreen), onDismiss: {
                        self.navigator.pop()
                    }) {
                        fullScreenView()
                    }
            }
```
