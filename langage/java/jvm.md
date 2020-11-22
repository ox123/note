### gc日志分析

- https://www.redhat.com/en/blog/collecting-and-reading-g1-garbage-collector-logs-part-2#

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

- Eden->S0, 为啥是复制而不是移动？

- 元数据区  
  - -XX:MaxMetaspaceSize=256m  
- -XX： ParallelGCThreads=N 来指定 GC 线程数， 其默认值为 CPU 核心数。  

##### CMS GC(Mostly Concurrent Mark and Sweep Garbage Collector  )

- -XX： +UseConcMarkSweepGC
  其对年轻代采用并行 STW 方式的 mark-copy (标记-复制)算法，对老年代主要使用并发 marksweep (标记-清除)算法。
  CMS GC 的设计目标是避免在老年代垃圾收集时出现长时间的卡顿，主要通过两种手段来达成此
  目标：
  1. 不对老年代进行整理，而是使用空闲列表（ free-lists） 来管理内存空间的回收。
  2. 在 mark-and-sweep （ 标记-清除） 阶段的大部分工作和应用线程一起并发执行。也就是说，在这些阶段并没有明显的应用线程暂停。但值得注意的是，它仍然和应用线程争抢CPU 时间。默认情况下， CMS 使用的并发线程数等于 CPU 核心数的 1/4。
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