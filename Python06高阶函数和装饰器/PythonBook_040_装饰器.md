# 装饰器

## 先来看个例子

定义一个加法函数
```python
def add(x,y):
    print('add function {} {}'.format(x,y)) # 日志记录
    return x+y
```
但是其中是print是非计算类的作用，我们可以定义一个专门记录日志的函数
```python
def logger(fn):
    def wrapper(*args,**kwargs):
        print("Start,The function {} help addition method about {} {}".format(fn.__name__,*args,**kwargs))
        return fn(*args,**kwargs)
    return wrapper
```

上面的logger函数调用后，会返回wrapper这个函数对象，并且这个函数经过柯里化后更好使用
```python
add = logger(add)
print(add(4,5))

结果如下
Start,The function add help addition method about 4 5
9
```

下面注意了，可以利用装饰器的语法糖，简化一下
```python
def logger(fn):
    def wrapper(*args,**kwargs):
        print("Start,The function {} help addition method about {} {}".format(fn.__name__,*args,**kwargs))
        ret = fn(*args,**kwargs)
        print("Stop")
        return ret
    return wrapper

@logger # add=logger(add)
def add(x,y):
    return x+y
print(add(4,5))

结果如下
Start,The function add help addition method about 4 5
Stop
9
```
>相当于将下面被装饰的函数的标识符作为实参传入上面的函数，并且无参装饰器等于单形参函数


### 计时器
写一个装饰器给函数使用，来计算函数运行时间
```python

def count(fn):
    import datetime
    def wrapper(*args,**kwargs):
        start = datetime.datetime.now()
        ret = fn(*args,**kwargs)
        delte = (datetime.datetime.now()-start).total_seconds()
        print("The Function:{} take {:.2f} seconds".format(fn.__name__,delte))
        return ret
    return wrapper
@count
def method(x,y):
    return x**y

print(method(2,100))

结果如下
The Function:method take 0.00 seconds
1267650600228229401496703205376
```

>刚才这个例子也叫非侵入式装饰,并没有破坏原函数

## 文档字符串
```python
def fn():
    """This is a function tell the method of doc"""

print(fn.__doc__)
```

## 修改装饰器的属性
```python
def logger(fn):
    def wrapper(*args,**kwargs):
        """THe is function about wrapper"""
        print("Start,The function : {}".format(fn.__name__))
        ret = fn(*args,**kwargs)
        print("Stop")
        return ret
    def inner(wrapper,fn):
        wrapper.__name__ = fn.__name__
        wrapper.__doc__ = fn.__doc__
    inner(wrapper,fn)
    return wrapper

@logger
def add(x,y):
    """This is function about add"""
    return x+y

print(add(4,9),add.__doc__,add.__name__)
```
其实这个inner()函数很通用，所以没必要放在内部，直接当作全局函数

```python
def copy_properties(dest,src):
    dest.__name__ = src.__name__
    dest.__doc__ = src.__doc__
def logger(fn):
    def wrapper(*args,**kwargs):
        """THe is function about wrapper"""
        print("Start,The function : {}".format(fn.__name__))
        ret = fn(*args,**kwargs)
        print("Stop")
        return ret
    copy_properties(wrapper,fn)
    return wrapper

@logger
def add(x,y):
    """This is function about add"""
    return x+y

print(add(4,9),add.__doc__,add.__name__)
```
继续优化，柯里化

```python
def copy_properties(dest):
    def inner(src):
        dest.__name__ = src.__name__
        dest.__doc__ = src.__doc__
    return inner
def logger(fn):
    def wrapper(*args,**kwargs):
        """THe is function about wrapper"""
        print("Start,The function : {}".format(fn.__name__))
        ret = fn(*args,**kwargs)
        print("Stop")
        return ret
    copy_properties(wrapper)(fn)
    return wrapper

@logger
def add(x,y):
    """This is function about add"""
    return x+y

print(add(4,9),add.__doc__,add.__name__)
```

继续优化，装饰器语法糖
```python
def copy_properties(src):
    def inner(dest):
        dest.__name__ = src.__name__
        dest.__doc__ = src.__doc__
        return dest
    return inner

def logger(fn):
    @copy_properties(fn) # wrapper = copy_properties(fn)(wrapper)
    def wrapper(*args,**kwargs):
        """THe is function about wrapper"""
        print("Start,The function : {}".format(fn.__name__))
        ret = fn(*args,**kwargs)
        print("Stop")
        return ret
    return wrapper

@logger
def add(x,y):
    """This is function about add"""
    return x+y
print(add(4,9),add.__doc__,add.__name__)
```

好了，这个copy_properties函数其实已经有现成的了，了解一下
```python
def logger(fn):
    def wrapper(*args,**kwargs):
        print("Start")
        ret = fn(*args,**kwargs)
        print("Stop")
        update_wrapper(wrapper=wrapper,wrapped=fn)
        return ret
    return wrapper
@logger
def add(x,y):
    return  x+y

print(add(44,55))
```

继续优化，不对update_wrapper使用优化，使用wraps

```python
from functools import update_wrapper,wraps
def logger(fn):
    @wraps(fn) # 等价于wraps(fn)(wrapper) 等价于 update_wrapper(wrapperd=fn,wrapper=wrapper)
    def wrapper(*args,**kwargs):
        return fn(*args,**kwargs)
    return wrapper
@logger
def add(x,y):
    return x+y

print(add(21,33))
```

## 计数器进阶
下面是一个计数器的功能，但是判断的部分可以解耦，封装成为一个单独的函数
```python
def logger(fn):
    @functools.wraps(wrapped=fn) # 等价于wrapper=wraps(fn)(wrapper)等价于functools.wrapper(wrapped=fn,wrapper=wrapper)
    def wrapper(*args,**kwargs):
        start_time = datetime.datetime.now()
        ret = fn(*args,**kwargs)
        delta_time = (datetime.datetime.now()- start_time).total_seconds()
        print("So fast") if delta_time < 5 else print("{} cost:{:.2f}s".format(fn.__name__,delta_time))
        return ret
    return wrapper
@logger
def add(x,y):
    time.sleep(6)
    return x+y
add(4,5)

结果如下
add cost:6.00s
```

继续修改，把判断条件作为变量
```python
import datetime,functools,time

def logger(fn,judge_config=5):
    @wraps(fn)
    def wrapper(*args,**kwargs):
        start_time = datetime.datetime.now()
        ret = fn(*args,**kwargs)
        spend_time = (datetime.datetime.now()-start_time).total_seconds()
        print("Too Fast!!!") if spend_time < judge_config else print("The {} function cost {:.2f}s".format(fn.__name__,spend_time))
        return ret
    return wrapper

@logger
def add(x,y):
    time.sleep(6)
    return x+y
add(5,8)

结果如下
The add function cost 6.00s
```
但是有个问题，我们这样写就没有办法来修改参数
所以继续柯里化
```python
import datetime,functools,time

def logger(judge_config):
    def _logger(fn):
        @wraps(fn)
        def wrapper(*args,**kwargs):
            start_time = datetime.datetime.now()
            ret = fn(*args,**kwargs)
            spend_time = (datetime.datetime.now()-start_time).total_seconds()
            print("Too Fast!!!") if spend_time < judge_config else print("The {} function cost {:.2f}s".format(fn.__name__,spend_time))
            return ret
        return wrapper
    return _logger

@logger(judge_config=3) # 等价于add=logger(judge_config)(add)等价于_logger(add)等价于wrapper
def add(x,y):
    time.sleep(4)
    return x+y
add(5,8)
```
所以，这就是代参装饰器，但是_logger()必须是单参！！！

这时候，还要解耦这个judge模块
```python
import datetime,functools,time


def method(fn,spend_time,judge_config):
    print("Too Fast!!!") if spend_time < judge_config else print(
        "The {} function cost {:.2f}s".format(fn.__name__, spend_time))

def logger(judge_config=3,fun=method):
    def _logger(fn):
        @wraps(fn)
        def wrapper(*args,**kwargs):
            start_time = datetime.datetime.now()
            ret = fn(*args,**kwargs)
            spend_time = (datetime.datetime.now()-start_time).total_seconds()
            fun(fn,spend_time,judge_config)
            return ret
        return wrapper
    return _logger

@logger(judge_config=3) # 等价于add=logger(judge_config)(add)等价于_logger(add)等价于wrapper
def add(x,y):
    time.sleep(4)
    return x+y
add(5,8)

结果如下
The add function cost 4.00s
```

## 总结
### 带参装饰器
- 是一个函数
- 函数作为它的形参
- 返回值是一个不带参的装饰器
- 使用@functionname(参数列表)方式调用
- 可以看作在装饰器外又加了一层函数，这个函数可以多参

### functools.update_wrapper(wrapper,wrapped,assigned=WRAPPER_ASSIGNMENTS,updated=WRAPPER_UPDATES)
-类似copy_properties功能
- wrapper包括函数，被更新者，wrapped被包装函数，数据源
- 元组WRAPPER_ASSIGNMENTS中是要被覆盖的属性'__module__', '__name__', '__qualname__', '__doc__','__annotations__'(模块名，名称，限定名，文档，参数注解)
- 元组WRAPPER_UPDATES中是要被覆盖的属性，__dict__(属性字典)
- 增加一个__wrapped__属性，保留着wrpped函数
