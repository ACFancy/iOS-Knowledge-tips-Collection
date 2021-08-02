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
### crash dump 技术书籍文档
 - [中文文档地址](https://faisalmemon.github.io/ios-crash-dump-analysis-book/zh)
 - [英文文档地址](https://faisalmemon.github.io/ios-crash-dump-analysis-book/en)
 - [git Repo地址](https://github.com/faisalmemon/ios-crash-dump-analysis-book)
### 锁屏绘制导致的crash
 - [苹果文档](**https://developer.apple.com/library/archive/documentation/NetworkingInternetWeb/Conceptual/NetworkingOverview/CommonPitfalls/CommonPitfalls.html)
 - ```c
   setsockopt(sock, SOL_SOCKET, SO_NOSIGPIPE, &value, sizeof(value));
   ```
### 常见异常类型
- Bad Memory Access [EXC_BAD_ACCESS // SIGSEGV // SIGBUS]
  - 进程试图访问无效的内存空间，或尝试访问的方法不允许(例如去写只读的内存空间)
- Abnormal Exit [EXC_CRASH // SIGABRT]
  - 进程异常退出，常见原因是uncaught Objective-C/C++ exception 并且调用了abort()
- Trace Trap [EXC_BREAKPOINT // SIGTRAP]
  - 和Abnormal Exit类似，这种异常是由于在特殊的节点加入debugger调试节点的原因。
- Illegal Instruction [EXC_BAD_INSTRUCTION // SIGILL]
  - 尝试执行一个非法或者未定义的指令时会触发该异常
- Quit [SIGQUIT]
  - 这个异常是由于其他进程拥有高优先级且可以管理本进程所导致（被高优先级进程kill掉）。
- Killed[SIGKILL]
  - 进程收到系统指令被干掉，可以自行查看Termination Reason来定位线程被干掉的原因。
- Guarded Resource Violation [EXC_GUARD]
  - 进程访问了一个被保护的资源。
- Resource Limit [EXC_RESOURCE]
  - 进程的资源超过限定阈值，表示进程占用太多资源，Exception Subtype会提示原因，例如
    - MEMORY:暗示了进程占用已经超过系统限制。如果之后出现由于系统占用过多进程被Kill，可能和这有关
    - WAKEUP:暗示线程每秒被进程唤醒太多次了，进而导致CPU被频繁唤醒并且造成电量损耗。 通常，这种事发生在线程间通信（通过peformSelector:onThread:或者dispatch_async），而且会远比预想的发生的更频繁。因为发生这种异常的通信被触发的如此频繁，所以很多后台线程会出现彼此高度雷同的堆栈信息——恰恰暗示了它们是从哪儿来的。
- Other Exception Types
  - 有些report可能出现无名的Exception Type，取而代之出现的是16进制的地址(0x12387617823)，下面列举一些
    - 0xbaaaaaad: 则说明此条logs是系统堆栈快照，并非crash report。可以通过同时按（手机）侧边按钮和音量键来记录堆栈快照。通常情况下，这些logs是用户无意中生成的，并非表示错误
    - 0xbad22222: 表示一个VoIP应用因为频繁暂停被iOS系统终止掉。
    - 0x8badf00d:（读起来像badfood）则说明一个应用因为触发了看门狗机制被iOS系统终止掉，有可能是应用花了太长时间启动，终止，或者是响应系统事件。一种常见原因是在主线程上做网络同步逻辑。不论Thread0上（也就是主线程）想做什么（重要的事），都应该转移到后台线程，或者换一种方式触发，这样它才不会阻塞主线程。
    - 0xc00010ff: 则说明app因为环境过热（的事件）被iOS系统干掉了。这个也许是和发生crash的特定设备有关，或者是和它所在的环境有关。
    - 0xdead10cc: (读起来像deadlock)则说明一个应用被系统终止掉，原因是在应用挂起时拿到了文件锁或者sqlite数据库所长期不释放直到被冻结。如果你的app在挂起时拿到了文件锁或者sqlite数据库锁，它必须请求额外的后台执行时间(request additional background execution time )并在被挂起前完成解锁操作。
    - 0x2bad45ec: 则说明app因为违规操作（安全违规）被iOS系统终止。终止描述会写：“进程被查到在安全模式进行非安全操作”，暗示app尝试在禁止屏幕绘制的时候绘制屏幕，例如当屏幕锁定时。用户可能会忽略这种异常，尤其当屏幕是关闭的或者当这种终止发生时正好锁屏
- 低内存Low Memory Reports
  - 当系统检测到内存不足时，虚拟内存系统会协同各应用来做内存释放，各个应用都会接受到内存警告，要求释放内存空间。如果内存依然不够，则你的应用会被终止，并生成report 存储在设备中，没有堆栈信息，可能的原因有一下几个：
    -  per-process-limit]:进程占用超过了它的最大内存值。每一个进程在常驻内存上的限制是早已经由系统为每个应用分配好了的。超过这个限制会导致进程被系统干掉。
    -  vm-pageshortage]/[vm-thrashing]/[vm]:由于系统内存压力被干掉。
    -  vnode-limit]: 打开太多文件了。
    -  highwater]:一个系统守护进程超过过了它的内存占用高水位（就是已经很危险了）。
    -  jettisoned]:进程因为其它不可描述的原因被杀掉。

### 参考
- [博客](https://byronjia.github.io/2020/12/iOS-Crash-%E5%88%86%E6%9E%90/)
 
