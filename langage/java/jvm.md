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

