###  EXC_CRASH (SIGSEGV) 
 - 线程间调用方法问题
   - 线程一调用了不在此线程的方法，导致内存引用出错 

### EXC_CRASH (SIGKILL)
####  Background Task crash [参考文章](http://www.cocoachina.com/ios/20180911/24864.html)
 - 0xdead10cc
   - crash头主要如下
      ```
      Exception Type:  EXC_CRASH (SIGKILL)

      Exception Codes: 0x0000000000000000, 0x0000000000000000

      Exception Note:  EXC_CORPSE_NOTIFY

      Termination Reason: Namespace SPRINGBOARD, Code 0xdead10cc

      Termination Description: SPRINGBOARD, com.xxx.xxx was task-suspended with locked system file
      ```
  
    - 当你的 App 有 Extension，而且 Extension 存在和 Host App 共享数据的需求，一般做法会将 db 文件放入 shared container 目录下，此时你的 App 就有大概率会发生这种 crash
    - App 进入后台运行 Background Task，end 之后 App 被系统 suspend，如果 suspend 之后还存在任何访问 db 的操作，此时 App 会立马被系统强杀，这是 Apple 出于保护数据库文件的完整的考虑
    - 所以正确的做法是将所有有可能在 App 进入后台之后，还会发生的 db 操作统统封入 Background Task，以确保安全。这个代码写在 db layer 可能更加合适
    - 而且 Apple 推荐当你想启动 Background Task 的时候，其实并不需要考虑当前 App 是出于 foreground 还是 background，即使 App 在前台启动 Background Task，也并不会占用进入后台之后 3 分钟额度，所以放心大胆的把关键代码放进 Background Task 吧
 - 0xbada5e47
   - crash头主要如下
       ```
       Exception Type:  EXC_CRASH (SIGKILL)

       Exception Codes: 0x0000000000000000, 0x0000000000000000

       Exception Note:  EXC_CORPSE_NOTIFY

       Termination Reason: Namespace ASSERTIOND, Code 0xbada5e47
       ```
   - 同理也是和 Background Task 相关，原因是 Apple 认为你启动了过多的 Background Task，所以要杀掉。
   - 少算多呢？几十个不多，当前的 threshold 是 1000 个，超过 1000 个才会强杀。
   - 如果你的 Background Task 封装发生在 db layer，出现大量数据过来需要存储或读取的时候，还是有可能会 hit 这个 limit
   - Background Task 在超时之后会调用 expiry handler，无论你有多少个 Background Task，所有 expiry handler 执行的时间不能超过若干秒，一旦超过也会被强杀
   - 在 expiry handler 里面切忌有任何比如 disk io 的耗时操作
   
 - 0x8badf00d
    - crash头主要如下
       ```
       Exception Type:  EXC_CRASH (SIGKILL)

       Exception Codes: 0x0000000000000000, 0x0000000000000000

       Exception Note:  EXC_CORPSE_NOTIFY

       Termination Reason: Namespace SPRINGBOARD, Code 0x8badf00d
       ```
    - 大家都很熟悉了，当你的主线程卡住的时间太长，系统的 Watchdog 会将你的 App 强杀，并生成一个带有 0x8badf00d 的 crash 日志
    - 当你的代码逻辑会产生 leaked Background Task 时，就会出现上面的系统强杀 crash 日志了，什么是 leaked Background Task 呢？看代码
       ```objc
       - (void)startBgTask
       {
         self.bgTaskID = [[UIApplication sharedApplication] beginBackgroundTaskWithExpirationHandler:^{
            NSLog(@"Expired: %lu", (unsigned long)self.bgTaskID);
            [[UIApplication sharedApplication] endBackgroundTask:self.bgTaskID];
         }];
        }

       - (void)endBgTask
       {
        [[UIApplication sharedApplication] endBackgroundTask:self.bgTaskID];
       }
       ```
     - 上面的代码如果 startBgTask 执行两次，就一定会出现 leaked Background Task，因为 self.bgTaskID 第二次会被赋予一个新的 ID，之前的 task ID 就丢失了，无法正确调用 end
     - 那怎么判断 0x8badf00d 到底是主线程卡死，还是出现了 leaked Background Task ？很简单，看主线程的 stack，如果长这样
       ```
       Thread 0 Crashed:0   

       libsystem_kernel.dylib        0x000000018472be08 0x18472b000 + 35921   

       libsystem_kernel.dylib        0x000000018472bc80 0x18472b000 + 32002   

       CoreFoundation                0x0000000184c6ee40 0x184b81000 + 9744003   

       CoreFoundation                0x0000000184c6c908 0x184b81000 + 9648724   

       CoreFoundation                0x0000000184b8cda8 0x184b81000 + 485525   

       GraphicsServices              0x0000000186b6f020 0x186b64000 + 450886   

       UIKit                         	0x000000018eb6d78c 0x18e850000 + 32664447   

       Messenger                     	0x0000000103015ee4 0x102ff8000 + 1225968   

       libdyld.dylib                 	0x000000018461dfc0 0x18461d000 + 4032
       ```
      - 这个 stack 很经典，经常会看到，不需要 symbolicate 也能知道是干啥，这是 UI 线程 runloop 处于 idle 状态的 stack，在等待 kernel 的 message。表示 UI 线程此时处于闲置状态，这种状态下的系统强杀大概率是由于 leaked Background Task 导致的
 
