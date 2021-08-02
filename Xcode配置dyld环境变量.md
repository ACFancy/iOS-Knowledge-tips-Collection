### dyld环境变量
- 苹果APP启动，分为两个过程：系统dylib动态链接库 app的main函数启动过程。main函数过程直接对iOS开发者。这里备忘的dylib过程
  - dyld加载到虚拟内存
    - load dylibs image
    - Rebase image
    - Bind image
    - Objc setup
    - initializers
  - runtime接管imageLoader加载到内存的镜像
    - imageLoader加载进行到内存
    - runtime 解析和处理
    - 按照继承关系与category关系，层级调用+load方法
- 配置Scheme,Environment Variables 对应表格如下：
   | 变量    | 值     | 说明    |
   | ------ | ------ | ------ |
   | DYLD_PRINT_STATISTICS | 1 | Print launch performance statistics 打印启动时间等参数 |
   | DYLD_PRINT_SEGMENTS   | 1 | Log segment mapping 打印segment映射日志|
   | DYLD_PRINT_INITIALIZERS | 1 | Log image initialization calls 打印镜像初始化调用日志|
   | DYLD_PRINT_BINDINGS   | 1 | Log symbol bindings 打印符号绑定日志|
   | DYLD_PRINT_APIS   | 1 | Log dyld API calls (for example, dlopen)打印 dyld API 调用日志|
   | DYLD_PRINT_ENV   | 1 | Print launch-time environment variables|
   | DYLD_PRINT_OPTS   | 1 | Print launch-time command line arguments|
   | DYLD_PRINT_LIBRARIES_POST_LAUNCH   | 1 | Log library loads , but only after main has run|
   | DYLD_PRINT_LIBRARIES   | 1 | Log library loads|
   | DYLD_IMAGE_SUFFIX   | 1 | Search for libraries with this suffix first|
- 参考
  - [博客](https://www.cnblogs.com/lxlx1798/p/9283956.html)
