### UIScrollView中有UIButton。 UIButton点击有问题（前几次点击无效）
- 1. contentSize很大
- 2. 通过代理scrollViewDidScroll(:)控制scrollView只能滑动到某个最大的位置
- 3. 设置最大的contentOffset值
- 4. 此时不要直接属性赋值，会导致button点击事件出问题（可能有动画导致子view的点击事件失效）
- 5. 需要设置 setContentOffset(: animated: false)来解决这个问题
