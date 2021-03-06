# 递归函数

## 函数的执行过程

```Python
def foo1(*args):
    print("foo1")
def foo2():
    foo3()
    print("foo2")
def foo3():
    print("foo3")
def main():
    print("main")
    foo1(100,101)
    foo2()
main()
```
- 全局帧中生成foo1、foo2、foo3、main函数对象
- main函数调用
- main中查找print函数压栈，将常量字符串压栈，调用print函数，弹出栈顶
- main函数全局查找foo1函数，将常量100、101压栈，调用函数foo1，创建栈帧。print函数压栈，字符串压栈，调用函数，弹出栈顶，返回值
- main函数全局查找foo2函数，调用foo2，创建栈帧，foo3函数压栈，调用foo3，创建栈帧。foo3完成print函数调用后返回，foo2恢复调用，执行print后返回值，main函数中执行完foo2弹出栈顶，main函数返回

>注意，两次完全相同的函数调用就是两次压栈，因为每次函数调用都是不同的

### 函数的字节码

```Python
def foo1(b,b1=3):
    print("foo1 called",b,b1)
    foo2()

def foo2(a):
    pass
```

分析下字节码
```Python
0   LOAD_GLOBAL     0(print)
3   LOAD_CONST      1('foo1 called')
6   LOAD_FAST       0(b)
9   LOAD_FAST       1(b1)
12  CALL_FUNCTION   3(3 positional,0keyword pair)
# CALL_FUCTION 是调用函数，调用完成后，弹出所有函数参数，函数本身关闭堆栈，并推出返回值
15  POP_TOP         # 删除顶部项目

16  LOAD_GLOBAL     1(foo2)
19  LOAD_CONST      2(2)
22  CALL_FUCTION        1(1 positional,0 keyword pair)
25  POP_TOP         
26  LOAD_CONST      0(None)
29  RETURN_VALUE    
```






## 递归Recursion
- 函数直接或间接调用自身就是 **递归**
- 递归需要有边界条件、递归前进段，递归返回段
- 递归一定要有边界条件
- 当边界条件不满足时，递归前进
- 当边界条件满足时，递归返回

>前面介绍的压栈规程，递归如果不给边界，就会造成栈溢出   
前段时间的ssl协议的漏洞就是没有检查栈的边界条件造成栈溢出


来，写个死循环。。。
```Python
def foo():
    foo()
    print("********")

foo()

结果如下
Traceback (most recent call last):
  File "D:/Gitee/python/test/matrix.py", line 57, in <module>
    foo()
  File "D:/Gitee/python/test/matrix.py", line 54, in foo
    foo()
  File "D:/Gitee/python/test/matrix.py", line 54, in foo
    foo()
  File "D:/Gitee/python/test/matrix.py", line 54, in foo
    foo()
  [Previous line repeated 996 more times]
RecursionError: maximum recursion depth exceeded

import sys
print(sys.getrecursionlimit())

结果如下
1000
```
所以，递归一定要有边界条件

### 斐波那契额数列
- 普通写法
```Python
a,b = 0,1
for i in range(5):
    a,b=b,a+b
print(a)
```

- 递归
```Python
def fibonacci(n):
    if n < 3:
        return 1
    return fibonacci(n-1)+fibonacci(n-2)

print(fibonacci(4))
```
>看看是怎么实现的？
| 执行过程 |
| -------- |
| fib(2)   |
| fib(3)   |
| fib(4)         |
- fib(4)压栈，执行fib(4),然后fib(3)和fib(2)压栈
- 执行fib(3),fib(2)和fib(1),执行完毕fib(2)和fib(1)
- 从栈顶弹出fib(3)和fib(2)
- fib(4)从栈顶弹出
- 空栈


- 可以看出来，这个东西虽然很美，但是还是效率很差！
- 但是如果能解决这些大量的重复计算，其效率是很好的

### 递归的要求
- 递归一定要有退出条件，递归调用一定要执行到这个条件退出，没有退出条件的递归，就是无限调用
- 递归调用的深度不宜过深
    - python对递归调用的深度做了限制，以保护解释器
    - 超过递归深度限制，抛出RecursionError：maxinum recursion depth exceeded 超出最大深度
    - sys.getrecursionlimit()可以查看深度限制

### 递归的性能
- 循环稍微复杂一些，但是只要不是死循环，可以多次迭代直至算出结果
- fibonacci函数代码简易懂，但是只要能获取到最外层的函数调用，内部递归结果都是中间结果，而且给定一个n都要进行2n次的递归，深度越深，效率越低，为了获取fibonacci数列需要外面再套一个n次的循环，效率就更低了
- 递归还有深度限制，如果递归复杂，函数反复压线，栈内存很快就溢出了
- 思考： 这个极简的递归代码能否提高性能呢？


## 递归总结
- 递归是一种很自然的表达，符合逻辑思维
- 递归相对运行效率低，每一次调用函数都要开辟栈帧
- 递归有深度限制,如果递归深度太深，函数反复压栈，栈内存很快溢出
- 如果是有限次的递归，可以使用递归调用，或者使用循环代替，循环代码稍微复杂一些，但是只要不是死循环，可以多次迭代得出结果
- 绝大多数递归调用，都可以使用循环代替
- 即使递归代码简单，但是能不用则不用递归


## 练习
### 练习一
求n的阶乘

- 利用公式的方式构架递归函数
```Python
def fn(n):
    if n == 1:
        return 1
    return n*fn(n-1)
```

- 利用循环的方式

```Python
# 循环的模式
count = 5
result = 1
for i in range(count,0,-1):
    result = result * count
print(result)

# 把循环写入递归
def fn(count,result=1):
    if count == 1:
        return result
    result = result * count
    return fn(count-1,result)
```

### 练习二
将一个数逆序放入列表，例如1234=>[4,3,2,1]

- 字符串操作
```Python

def revert(date='1234',lst=None):
    if not lst:
        lst = []
    lst.append(date[-1])
    if len(date) == 1:
        return lst
    return revert(date[:-1],lst)

print(revert())

```
- 数字操作

```python
def revert(date=1234,lst=None):
    if lst is None:
        lst = []
    lst.append(date % 10)
    if date < 10:
        return lst
    return revert(int(date/10),lst)

print(revert())
```
### 练习三
猴子吃桃问题
>猴子第一天摘下若干个桃子，当即吃了一半，还不过瘾，又多吃了一个，第二天早上又将剩下的桃子吃了一半，又多吃了一个，到第十天，只剩下一个桃子，求第一天有多少桃子

- 公式
```Python
def peach(day=10):
    if day == 1:
        return 1
    return 2*(peach(day-1)+1)
print(peach())
```
- 循环改递归
```Python
# 循环
result = 1
for i in range(9):
    result = (result+1)*2
print(result)

# 递归
def peach(day=10,result=1):
    if day == 1:
        return result
    result = (result+1)*2
    return peach(day-1,result)
print(peach())
```
