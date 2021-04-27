## UILabel, UIButton
```Objc
    [控件 setContentHuggingPriority:UILayoutPriorityRequired forAxis:UILayoutConstraintAxisHorizontal];
    [控件 setContentCompressionResistancePriority:UILayoutPriorityRequired forAxis:UILayoutConstraintAxisHorizontal];
```
## setNeedsLayout、layoutIfNeeded、layoutSubViews
- layoutSubviews总结
```objc
// ios layout机制相关方法
- (CGSize)sizeThatFits:(CGSize)size
- (void)sizeToFit

- (void)layoutSubviews
- (void)layoutIfNeeded
- (void)setNeedsLayout

- (void)setNeedsDisplay
- (void)drawRect
```
- layoutSubviews在以下情况下会被调用：

  - init初始化不会触发layoutSubviews
    - 但是是用initWithFrame 进行初始化时，当rect的值不为CGRectZero时,也会触发

  - addSubview会触发layoutSubviews

  - 设置view的Frame会触发layoutSubviews，当然前提是frame的值设置前后发生了变化

  - 滚动一个UIScrollView会触发layoutSubviews

  - 旋转Screen会触发父UIView上的layoutSubviews事件

  - 改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件
- layoutSubviews, 当我们在某个类的内部调整子视图位置时，需要调用
- 刷新子对象布局
    - layoutSubviews方法：这个方法，默认没有做任何事情，需要子类进行重写
    - setNeedsLayout方法： 标记为需要重新布局，异步调用layoutIfNeeded刷新布局，不立即刷新，但layoutSubviews一定会被调用
    - layoutIfNeeded方法：如果，有需要刷新的标记，立即调用layoutSubviews进行布局（如果没有标记，不会调用layoutSubviews）
    - 如果要立即刷新，要先调用[view setNeedsLayout]，把标记设为需要布局，然后马上调用[view layoutIfNeeded]，实现布局
    - 在视图第一次显示之前，标记总是“需要刷新”的，可以直接调用[view layoutIfNeeded]
- 重绘
```objc
-drawRect:(CGRect)rect方法：重写此方法，执行重绘任务
-setNeedsDisplay方法：标记为需要重绘，异步调用drawRect
-setNeedsDisplayInRect:(CGRect)invalidRect方法：标记为需要局部重绘
```
  - sizeToFit会自动调用sizeThatFits方法
  - sizeToFit不应该在子类中被重写，应该重写sizeThatFits
  - sizeThatFits传入的参数是receiver当前的size，返回一个适合的size
  - sizeToFit可以被手动直接调用
  - sizeToFit和sizeThatFits方法都没有递归，对subviews也不负责，只负责自己
- 总结
  - layoutSubviews对subviews重新布局
  - layoutSubviews方法调用先于drawRect
  - setNeedsLayout在receiver标上一个需要被重新布局的标记，在系统runloop的下一个周期自动调用layoutSubviews
  - layoutIfNeeded方法如其名，UIKit会判断该receiver是否需要layout.根据Apple官方文档,layoutIfNeeded方法应该是这样的
  - layoutIfNeeded遍历的不是superview链，应该是subviews链
  - drawRect是对receiver的重绘，能获得context
  - setNeedDisplay在receiver标上一个需要被重新绘图的标记，在下一个draw周期自动重绘，iphone device的刷新频率是60hz，也就是1/60秒后重绘 。

- 延伸
   - initWithFrame:前面已经见到程序创建UI控件时常常会调用该方法执行初始化因此如果你需要对UI控件执行一些额外的初始化即可通过重写该方法来实现
   - initWithCoder:程序通过在nib文件中加载完该控件后会自动调用该方法。因此如果程序需要在nib文件中加载该控件后执行自定义初始化则可通过重写该方法来实现。
   - drawRect:如果程序需要自行绘制该控件的内容则可通过重写该方法来实现。
   - layoutSubviews如果程序需要对该控件所包含的子控件布局进行更精确的控制可通过重写该方法来实现。
   - didAddSubview:当该控件添加子控件完成时将会激发该方法。
   - willRemoveSubview:当该控件将要删除子控件时将会激发该方法。
   - willMoveToSuperview:当该控件将要添加到其父控件中时将会激发该方法。
   - didMoveToSuperview当把该控件添加到父控件完成时将会激发该方法。
   - willMoveToWindow: 当该控件将要添加到窗口中时将会激发该方法。
   - didMoveToWindow当把该控件添加到窗口完成时将会激发该方法。
 - 参考
    - [博客](https://www.cnblogs.com/muzijie/p/7404336.html)

