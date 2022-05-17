#### UIBezierPath 居中相关
- SKShapeNode初始化 centered 为true的时候，SKShapeNode的path的等价于 path.apply(.init(translationX: -path.bounds.midX, y: -path.bounds.midY))
```swift
 //  centered = true 
 let path = UIBezierPath(roundedRect: CGRect(x: 0, y: 0, width: width, height: height), cornerRadius: height)
 let node = SKShapeNode(path: path.cgPath, centered: true)

// 等价于
let path = UIBezierPath(roundedRect: CGRect(x: 0, y: 0, width: width, height: height), cornerRadius: height)
path.apply(.init(translationX: -path.bounds.midX, y: -path.bounds.midY))
node.path = path.cgPath
```
