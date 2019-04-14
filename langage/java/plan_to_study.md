### Collections.singletonMap
### 反射初始化类
```java
/// Utils#newInstance
public static <T> T newInstance(Class<T> c) {
    if (c == null)
        throw new KafkaException("class cannot be null");
    try {
        return c.getDeclaredConstructor().newInstance();
    } catch (NoSuchMethodException e) {
        throw new KafkaException("Could not find a public no-argument constructor for " + c.getName(), e);
    } catch (ReflectiveOperationException | RuntimeException e) {
        throw new KafkaException("Could not instantiate class " + c.getName(), e);
    }
}
```