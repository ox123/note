### gc日志分析

- https://www.redhat.com/en/blog/collecting-and-reading-g1-garbage-collector-logs-part-2#
- [【GC分析】Java GC日志查看 - QiaoZhi - 博客园 (cnblogs.com)](https://www.cnblogs.com/qlqwjy/p/7929414.html)

### 类加载时机

1. 当虚拟机启动时，初始化用户指定的朱磊，就是启动执行的main方法所在的类；
2. 当遇到用到以新建目标类实例的new指令时，初始化new指令的目标类，就是new一个雷的时候要实例化；
3. 当遇到调用静态方法的指令时，初始化该静态方法所在的类
4. 当遇到范文静态字段的指令时，初始化该静态字段所在的类
5. 子类的初始化会触发父类的初始化；
6. 如果一个借口定义了default方法，那么直接实现或者间接实现该接口的类的初始化，会触发该接口的初始化
7. 使用反射API对某个类进行反射调用时，初始化这个类，跟前面一样，反射调用要么是已经有实例了，要么是静态方法，都需要初始化。
8. 当初次调用MethodHandle实例时，初始化该MethodHandle指向的方法所在的类

### 不会初始化，可能会加载

1. 通过子类引用父类的静态字段，只会触发父类的初始化，而不会触发子类的初始化；
2. 定义对象数组，不会触发该类的初始化；
3. 常量在编译期间会存入调用类的常量池中，本质上并没有直接引用定义常量的类，不会触发定义常量所在的类；
4. 通过类名获取Class对象，不会触发类的初始化，Hello.class不会让Hello类初始化；



### 类加载器

- BootstrapClassLoader
- ExtClassLoader
- AppClassLoader
- URLClassLoader

1. 双亲委托

2. 负责依赖

3. 缓存加载
   - 同一个类型只会加载一次
   - 缓存已经加载的类



### 自定义ClassLoader



### 添加引用类的几种方式

- 自定义CLassLoader加载
- 放到JDK的lib/ext下，或者-Djava.ext.dirs
- 

---

**java命令行汇总**

### jstack

- jstack -l pid

### jmap

- ```
  [root@iZ283bxmq9vZ ~]# jmap -heap 5750
  Attaching to process ID 5750, please wait...
  Debugger attached successfully.
  Server compiler detected.
  JVM version is 25.181-b13
  
  using thread-local object allocation.
  Mark Sweep Compact GC
  
  Heap Configuration:
     MinHeapFreeRatio         = 40
     MaxHeapFreeRatio         = 70
     MaxHeapSize              = 262144000 (250.0MB)
     NewSize                  = 5570560 (5.3125MB)   // 大概为物理内存的1/64 除以16
     MaxNewSize               = 87359488 (83.3125MB)
     OldSize                  = 11206656 (10.6875MB)
     NewRatio                 = 2
     SurvivorRatio            = 8
     MetaspaceSize            = 21807104 (20.796875MB)
     CompressedClassSpaceSize = 1073741824 (1024.0MB)
     MaxMetaspaceSize         = 17592186044415 MB
     G1HeapRegionSize         = 0 (0.0MB)
  
  Heap Usage:
  New Generation (Eden + 1 Survivor Space):
     capacity = 27459584 (26.1875MB)
     used     = 18430040 (17.576255798339844MB)
     free     = 9029544 (8.611244201660156MB)
     67.11696724903042% used
  Eden Space:
     capacity = 24444928 (23.3125MB)
     used     = 18208160 (17.364654541015625MB)
     free     = 6236768 (5.947845458984375MB)
     74.48645379524129% used
  From Space:
     capacity = 3014656 (2.875MB)
     used     = 221880 (0.21160125732421875MB)
     free     = 2792776 (2.6633987426757812MB)
     7.360043733016305% used
  To Space:
     capacity = 3014656 (2.875MB)
     used     = 0 (0.0MB)
     free     = 3014656 (2.875MB)
     0.0% used
  tenured generation:
     capacity = 60653568 (57.84375MB)
     used     = 53512648 (51.03363800048828MB)
     free     = 7140920 (6.810111999511719MB)
     88.22671075178957% used
  
  ```

- 默认为G1收集器，jdk < 8 为并行收集器，java jdk9 开始为G1

### jcmd

- jcmd 5750 help

  ```
  5750:
  The following commands are available:
  JFR.stop
  JFR.start
  JFR.dump
  JFR.check
  VM.native_memory
  VM.check_commercial_features
  VM.unlock_commercial_features
  ManagementAgent.stop
  ManagementAgent.start_local
  ManagementAgent.start
  VM.classloader_stats
  GC.rotate_log
  Thread.print
  GC.class_stats
  GC.class_histogram
  GC.heap_dump
  GC.finalizer_info
  GC.heap_info
  GC.run_finalization
  GC.run
  VM.uptime
  VM.dynlibs
  VM.flags
  VM.system_properties
  VM.command_line
  VM.version
  help
  
  ```

- jcmd 5750 VM.version

- jcmd 5750 VM.flags

### jjs

- jrunscript -e "cat (baidu.com)"

- 执行js片段

  ```
  jrunscript -e "print('hello,kk.jvm'+1)
  ```

- 执行js代码

  ```
  jrunscript -l js -f /XXX/XXX/test.js
  ```

### jconsole

### jvisualvm  

---

### GC

##### 引用计数

- 从对象的根出发，遍历到达根的对象。没有被标记的对象可以被回收

###### 标记清除算法(Mark and Sweep)

- 标记
- 清除
- 压缩 (Compact)

---

- 并行 GC 和 CMS 的基本原理。
  优势：可以处理循环依赖，只扫描部分对象  
- 默认15次gc还存活的对象移动到老年代
  - -XX:+MaxTenuringThreshold=15  
- Eden->S0, **为啥是复制而不是移动？**
  - 年轻代是复制
  - 老年代是移动
- 元数据区  
  - -XX:MaxMetaspaceSize=256m  
- -XX： ParallelGCThreads=N 来指定 GC 线程数， 其默认值为 CPU 核心数。  
- -XX： +UseParallelGC
  -XX： +UseParallelOldGC
  -XX： +UseParallelGC -XX:+UseParallelOldGC  

##### CMS GC(Mostly Concurrent Mark and Sweep Garbage Collector  )

- -XX： +UseConcMarkSweepGC
  其对年轻代采用并行 STW 方式的 mark-copy (标记-复制)算法，对老年代主要使用并发 marksweep (标记-清除)算法。
  CMS GC 的设计目标是避免在老年代垃圾收集时出现长时间的卡顿，主要通过两种手段来达成此
  目标：
  1. 不对老年代进行整理，而是使用空闲列表（ free-lists） 来管理内存空间的回收。
  2. 在 mark-and-sweep （ 标记-清除） 阶段的大部分工作和应用线程一起并发执行。也就是说，在这些阶段并没有明显的应用线程暂停。但值得注意的是，它仍然和应用线程争抢CPU 时间。默认情况下，**CMS 使用的并发线程数等于 CPU 核心数的 1/4**。
  3. 如果服务器是多核 CPU，并且主要调优目标是降低 GC 停顿导致的系统延迟，那么使用 CMS 是个很明智的选择。进行老年代的并发回收时，可能会伴随着多次年轻代的 minor GC。
     思考：并行 Parallel 与并发 Concurrent 的区别？  

- CMS 六个阶段， Concurrent开始的业务并没有暂停
  - Initial Mark 初始标记
  - Concurrent Mark 并发标记
  - Concurrent Preclean 并发预清理
  - Final Remark 最终标记     ----> STW
  - Concurrent Sweep 并发清除
  - Concurrent Reset  并发重置
  - 

- MaxHeapSize 默认分配机器的1/4 物理内存
- NewSize  物理内存的1/64
- MaxNewSize 对并行GC有效
- <8 并行，9是G1（CMS标记为过时）

### G1

- G1 GC 最主要的设计目标是：**将 STW 停顿的时间和分布，变成可预期且可配置的。 **

- -XX:+UseG1GC -XX:MaxGCPauseMillis=50  （默认值200）

- 事实上， G1 GC 是一款软实时垃圾收集器，可以为其设置某项特定的性能指标。为了达成可预期停顿时间的指标， G1 GC 有一些独特的实现。
  首先，堆不再分成年轻代和老年代，而是划分为多个（通常是 2048 个）可以存放对象的 小块堆区域(smaller heap regions)。每个小块，可能一会被定义成 Eden 区，一会被指定为 Survivor区或者Old 区。在逻辑上，所有的 Eden 区和 Survivor区合起来就是年轻代，所有的 Old 区拼在一起那就是老年代  

- -XX: +UseG1GC：启用 G1 GC；
  **-XX:G1NewSizePercent**：初始年轻代占整个 Java Heap 的大小，默认值为 5%；
  **-XX: G1MaxNewSizePercent**：最大年轻代占整个 Java Heap 的大小，默认值为 60%；
  **-XX: G1HeapRegionSize**：设置每个 Region 的大小，单位 MB，需要为 1， 2， 4， 8， 16， 32 中的某个值，默认是堆内存的 1/2000。如果这个值设置比较大，那么大对象就可以进入 Region 了。
  **-XX: ConcGCThreads**：与 Java 应用一起执行的 GC 线程数量，**默认是 Java 线程的 1/4（继承CMS）**，减少这个参数的数值可能会提升并行回收的效率，提高系统内部吞吐量。如果这个数值过低，参与回收垃圾的线程不足，也会导致并行回收机制耗时加长。
  **-XX:+InitiatingHeapOccupancyPercent**（简称 IHOP）： G1 内部并行回收循环启动的阈值，默认为 Java Heap的 45%。这个可以理解为老年代使用大于等于45% 的时候， JVM 会启动垃圾回收。这个值非常重要，它决定了在什么时间启动老年代的并行回收。
  **-XX:G1HeapWastePercent**： G1停止回收的最小内存大小，默认是堆大小的 5%。 GC 会收集所有的 Region 中的对象，但是如果下降到了 5%，就会停下来不再收集了。就是说，不必每次回收就把所有的垃圾都处理完，可以遗留少量的下次处理，这样也降低了单次消耗的时间。
  **-XX:G1MixedGCCountTarget**：设置并行循环之后需要有多少个混合 GC 启动，默认值是 8 个。老年代 Regions的回收时间通常比年轻代的收集时间要长一些。所以如果混合收集器比较多，可以允许 G1 延长老年代的收集时间。  

  -XX： +G1PrintRegionLivenessInfo：这个参数需要和 -XX:+UnlockDiagnosticVMOptions 配合启动，打印 JVM 的调试信息，每个 Region 里的对象存活信息。
  -XX： G1ReservePercent： G1 为了保留一些空间用于年代之间的提升，默认值是堆空间的 10%。因为大量执行回收的地方在年轻代（存活时间较短），所以如果你的应用里面有比较大的堆内存空间、比较多的大对象存活，这里需要保留一些内存。
  -XX： +G1SummarizeRSetStats：这也是一个 VM 的调试信息。如果启用，会在 VM 退出的时候打印出 Rsets 的详细总结信息。如果启用 -XX:G1SummaryRSetStatsPeriod 参数，就会阶段性地打印 Rsets 信息。
  -XX： +G1TraceConcRefinement：这个也是一个 VM 的调试信息，如果启用，并行回收阶段的日志就会被详细打印出来。
  -XX： +GCTimeRatio：这个参数就是计算花在 Java 应用线程上和花在 GC 线程上的时间比率，默认是 9，跟新生代内存的分配比例一致。这个参数主要的目的是让用户可以控制花在应用上的时间， G1 的计算公式是 100/（ 1+GCTimeRatio）。这样如果参数设置为 9，则最多 10% 的时间会花在 GC 工作上面。 Parallel GC 的默认值是 99，表示 1% 的时间被用在 GC 上面，这是因为 Parallel GC 贯穿整个 GC，而 G1 则根据 Region 来进行划分，不需要全局性扫描整个内存堆。
  -XX： +UseStringDeduplication：手动开启 Java String 对象的去重工作，这个是 JDK8u20 版本之后新增的参数，主要用于
  相同 String 避免重复申请内存，节约 Region 的使用。
  -XX： MaxGCPauseMills：预期 G1 每次执行 GC 操作的暂停时间，单位是毫秒，默认值是 200 毫秒， G1 会尽量保证控制在这个范围内  

### ZGC

- jdk > 11 只支持linux
- **-XX: +UnlockExperimentalVMOptions -XX:+UseZGC -Xmx16g**
  ZGC 最主要的特点包括:
  1. GC 最大停顿时间不超过 10ms
  2. 堆内存支持范围广，小至几百 MB 的堆空间，大至 4TB 的超大堆内存（ JDK13 升至 16TB）
  3. 与 G1 相比，应用吞吐量下降不超过 15%
  4.  当前只支持 Linux/x64 位平台， JDK15 后支持 MacOS 和Windows 系统  

---

选择正确的 GC 算法，唯一可行的方式就是去尝试，一般性的指导原则：

1. 如果系统考虑吞吐优先， CPU 资源都用来最大程度处理业务，用 Parallel GC；
2. 如果系统考虑低延迟有限，每次 GC 时间尽量短，用 CMS GC；
3. 如果系统内存堆较大，同时希望整体来看平均 GC 时间可控，使用 G1 GC。
   **对于内存大小的考量**
4. 一般 4G 以上，算是比较大，用 G1 的性价比较高。
5. 一般超过 8G，比如 16G-64G 内存，非常推荐使用 G1 GC。  

**所有 GC 算法，一共有 7 类**

1. 串行 GC（Serial GC）: 单线程执行，应用需要暂停；
2.  并行 GC（ParNew、Parallel Scavenge、Parallel Old）: 多线程并行地执行垃圾回收，关注与高吞吐；
3. CMS（Concurrent Mark-Sweep）: 多线程并发标记和清除，关注与降低延迟；
4. G1（G First）: 通过划分多个内存区域做增量整理和回收，进一步降低延迟；
5. ZGC（Z Garbage Collector）: 通过着色指针和读屏障，实现几乎全部的并发执行，几毫秒级别的延迟，线性可扩展；
6.  Epsilon: 实验性的 GC，供性能分析使用；
7. Shenandoah: G1 的改进版本，跟 ZGC 类似  

**GC 算法和实现的演进路线**

1. 串行 -> 并行: 重复利用多核 CPU 的优势，大幅降低 GC 暂停时间，提升吞吐量。
2. 并行 -> 并发： 不只开多个 GC 线程并行回收，还将GC操作拆分为多个步骤，让很多繁重的任务和应用线程一起并发执行，减少了单次 GC 暂停持续的时间，这能有效降低业务系统的延迟。
3. CMS -> G1： G1 可以说是在 CMS 基础上进行迭代和优化开发出来的，划分为多个小堆块进行增量回收，这样就更进一步地降低了单次 GC 暂停的时间
4.  G1 -> ZGC:： ZGC 号称无停顿垃圾收集器，这又是一次极大的改进。 ZGC 和 G1 有一些相似的地方，但是底层的算法和思想又有了全新的突破。

---

**脱离场景谈性能都是耍流氓**
      目前绝大部分 Java 应用系统，堆内存并不大比如 2G-4G 以内，而且对 10ms 这种低延迟的 GC 暂停不敏感，也就是说处而往往是我们追求的重点，这时候就需要考虑采用并行 GC。如果堆内存再大一些，可以考虑 G1 GC。如果内存非常大（比如超过 16G，甚至是 64G、 128G），或者是对延迟非常
敏感（比如高频量化交易系统），就需要考虑使用本节提到的新 GC（ ZGC/Shenandoah）。  

### GC 日志分析

- java -XX:+PrintGCDetails GCLogAnalysis
- java -Xloggc:gc.demo.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps  GCLogAnalysis  
- 并行gc，堆内存为物理内存的1/4

- **串行GC**: java -XX:+UseParallelGC -Xms512m -Xmx512m -Xloggc:gc.demo.log  -XX:+PrintGCDetails  -XX:+PrintGCDateStamps GCLogAnalysis  
- **CMS GC**:  java -XX:+UseConcMarkSweepGC -Xms512m -Xmx512m -Xloggc:gc.demo.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis  
- **G1 GC**: java -XX:+UseG1GC -Xms512m -Xmx512m -Xloggc:gc.demo.log  -XX:+PrintGCDetails -XX:+PrintGCDateStamps GCLogAnalysis  

### 工具

- fastthread
- 