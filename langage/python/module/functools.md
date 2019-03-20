### functools模块
是为了高阶函数（该高阶函数的定义为作用于或返回其它函数的函数）而设置的。一般来说，任何可调用的对象在该模块中都可被当做函数而处理。

- functools. cmp_to_key(func)
- @functools. lru_cache(maxsize=128, typed=False)
   装饰器生成一个可调用的内存来保存最近调用，这个调用不超过maxsize指定的大小，并用其来装饰函数。当需要使用相同的参数周期性的调用I/O绑定函数或高消耗函数时，它可以保存时间。
- functools. partial(func, *args, **keywords)
```python
>>>fromfunctoolsimport partial

>>>basetwo = partial(int, base=2)

>>>basetwo.__doc__='Convert base 2 string to an int.'

>>>basetwo('10010')

18
```