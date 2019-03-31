### 读取properties操作
```java
InputStream in = new BufferedInputStream(new FileInputStream(file));
properties = new Properties();
properties.load(in);
```