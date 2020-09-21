### iOS13三指撤销
- 解决方案
 - 将文案判长和截取异步添加到主队列，在下一个runloop执行。
    ```Objective-C
     - (void)textChange:(UITextField *)textField {
         dispatch_async(dispatch_get_main_queue(), ^{
         ... ...
         });
     }
    ```
 - 数字截断后 crash
    - 数字输入限制长度后，超过长度后继续输入，这个时候撤销也会产生crash，而且上面的方法不可行。目前想到的方案是在UITextField的回调方法进行输入的拦截。
    ```Objective-C
      - (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {
           /// 输入数字后截取字符串仍旧可以触发撤销操作导致crash, 在这里拦截一下
             if (textField.keyboardType == UIKeyboardTypeNumberPad
                && range.location >= textField.tt_maxLength) {
                  return NO;
             }
            return YES;
      }
    ```  
- 参考 [!链接](https://juejin.im/post/6844903952526344205)
