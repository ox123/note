### 单利模式

```java
public class Singleton {
    private Singleton(){}
    public static Singleton getInstance(){
        return SingletonHelper.instance;
    }
    private static class SingletonHelper{
        private static Singleton instance =new Singleton();
    }
}
```