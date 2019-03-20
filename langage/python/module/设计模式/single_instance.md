### 使用模块实现
python 的模块就是天然的单例模式，因为模块在第一次导入时，会生成 .pyc 文件，当第二次导入时，就会直接加载 .pyc 文件，而不会再次执行模块代码

### 使用 __new__
```python
class Singleton(object):
    _instance = None
    def __new__(cls, *args, **kw):
        if not cls._instance:
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kw)  
        return cls._instance  
class MyClass(Singleton):  
    a = 1
```

### 使用装饰器实现单例模式
```python
from functools import wraps
def singleton(cls):
    instances = {}
    @wraps(cls)
    def getinstance(*args, **kw):
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance
@singleton
class MyClass(object):
    a = 1
```
### 使用 metaclass
元类（metaclass）可以控制类的创建过程，它主要做三件事：
- 拦截类的创建
- 修改类的定义
- 返回修改后的类
```python
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]
# Python2
class MyClass(object):
    __metaclass__ = Singleton
# Python3
# class MyClass(metaclass=Singleton):
#    pass
```