### 代码
```swift
import UIKit
import ObjectiveC

public extension UIView {
    private struct AssociatedObjectKeys {
        static var effectGroup: String = "effectGroup"
    }

    @objc var effectGroup: UIMotionEffectGroup? {
        get {
            return objc_getAssociatedObject(self, &AssociatedObjectKeys.effectGroup) as? UIMotionEffectGroup
        }
        set {
            objc_setAssociatedObject(self, &AssociatedObjectKeys.effectGroup, newValue, .OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
    }

    @objc func add(xAxisValue xValue: CGFloat, yAxisValue yValue: CGFloat) {
        guard let effectGroup = effectGroup else {
            return
        }
        let xAxis = UIInterpolatingMotionEffect(keyPath: "center.x", type: .tiltAlongHorizontalAxis)
        xAxis.minimumRelativeValue = -abs(xValue)
        xAxis.maximumRelativeValue = abs(xValue)
        let yAxis = UIInterpolatingMotionEffect(keyPath: "center.y", type: .tiltAlongVerticalAxis)
        yAxis.minimumRelativeValue = -abs(yValue)
        yAxis.maximumRelativeValue = abs(yValue)
        effectGroup.motionEffects = nil
        removeMotionEffect(effectGroup)
        effectGroup.motionEffects = [xAxis, yAxis]
        addMotionEffect(effectGroup)
    }

    @objc func removeSelfMotionEffect() {
        guard let effectGroup = effectGroup else {
            return
        }
        self.removeMotionEffect(effectGroup)
    }
}
```
