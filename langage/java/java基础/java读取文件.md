### 读取properties操作

```java
InputStream in = new BufferedInputStream(new FileInputStream(file));
properties = new Properties();
properties.load(in);
```

### java NIO MappedByteBuffer（MMAP实现）

- https://www.jianshu.com/p/f90866dcbffc

- MMAP 测试与深入https://blog.csdn.net/zhxdick/article/details/81130102

- 深入分析：https://mp.weixin.qq.com/s?__biz=MzI0NzEyODIyOA==&mid=2247484211&idx=1&sn=1a2e597fe3a039a5925db5f000bad872&chksm=e9b58af8dec203ee578e8903fa7df7abbedcb3f90592117a8885179cb64c72366481427df91d&scene=21#wechat_redirect
  
  ```java
  // 读取文件
  public class MappedByteBufferTest {
    public static void main(String[] args) {
        File file = new File("D://data.txt");
        long len = file.length();
        byte[] ds = new byte[(int) len];
  
        try {
            MappedByteBuffer mappedByteBuffer = new RandomAccessFile(file, "r")
                    .getChannel()
                    .map(FileChannel.MapMode.READ_ONLY, 0, len);
            for (int offset = 0; offset < len; offset++) {
                byte b = mappedByteBuffer.get();
                ds[offset] = b;
            }
  
            Scanner scan = new Scanner(new ByteArrayInputStream(ds)).useDelimiter(" ");
            while (scan.hasNext()) {
                System.out.print(scan.next() + " ");
            }
  
        } catch (IOException e) {}
    }
  }
  ```

### 什么是IO （装饰器设计模式与适配器设计模式）

然而I/O可以分为数据格式和传输方式，而这两类正是影响效率的关键因素。  :pear:
数据格式分为：

- 字节：InputStream 和 OutputStream
- 字符：Writer 和 Reader

传输方式分为：

- 磁盘操作：File

- 网络操作：Socket

- Inputstream->InputStreamReader->Reader
  
  ```java
  //继承Reader，实现Reader的接口
  public class InputStreamReader extends Reader {
    //用于编解码的对象
    private final StreamDecoder sd;
    public InputStreamReader(InputStream in) {
        super(in);
        try {
            sd = StreamDecoder.forInputStreamReader(in, this, (String)null); // ## check lock object
        } catch (UnsupportedEncodingException e) {
            // The default encoding should always be available
            throw new Error(e);
        }
    }
    public int read() throws IOException {
        return sd.read();
    }
  }
  ```
  
  每一个InputStreamReader继承了Reader，然后通过```StreamDecoder```间接拥有了InputStream实例，因为byte到char需要编解码。
  所以InputStreamReader就是字节和字符之间的```适配器```，OutputStreamReader也是类似的实现。

### java IO分类

- 一些基础接口。
  例如：Closeable、Readable、DataInput。
- Reader/Writer的所有子类，这些都是字符读写的适配器，以及适配器的扩展。
  例如：InputStreamReader、FileReader、BufferedReader、CharArrayReader。
- InputStream/OutputStream的所有子类，这些都是字节读写的各种实例，是被装饰的对象。
  例如：FileInputStream、SocketInputStream、StringBufferInputStream、ByteArrayInputStream。
- FilterInputStream/FilterOutputStream的所有子类，这些都是装饰者，可以重复包装。
  例如：PushbackInputStream、BufferedInputStream、DataInputStream、LineNumberInputStream。
