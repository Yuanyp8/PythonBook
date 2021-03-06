# 函数的返回值和作用域
## 函数的返回值

每个函数的返回值只能有一个return起作用，一旦有一个return生效，其他的所有语句全部不执行
>注意：
    1. 函数在遇到return语句时，就会停止执行函数，并将值返回，即return语句代表函数执行结束
    2. 如果函数里没有return语句，就会默认返回None
    3. 如果想返回多个值: 则用return user_info, country  返回多个值时，其返回的值是以元组的形式返回。

- 只执行一个return
```python
def add(x,y):
    result = x + y
    print("Print:",result)
    if result >= 10:
        return result/10
    else:
        return result*10
    return "到底输不输出"
    print("到死print不print")


print(add(10,100))

结果如下
Print: 110
11.0
```

- 一个return返回多个值(实则是一个容器)
```python
def regex(x,y,*args):
    return max(x,y,*args),min(x,y,*args)

print(regex(1,2,6,9,3,5,0,3,5,8,3))

返回结果
(9，0)
```


- 函数有唯一出口
```python
def abc():
    x = 10

print(x)

返回结果
Traceback (most recent call last):
  File "D:/python/getlogwork/tes.py", line 4, in <module>
    print(x)
NameError: name 'x' is not defined
```
>上面的例子是单单创建了函数对象，并没有调用，并没有被执行！

继续看这个例子
```python
def abc():
    print("*"*10)
    x = 10
abc()
print(x)

结果如下
Traceback (most recent call last):
  File "D:/python/getlogwork/tes.py", line 5, in <module>
    print(x)
NameError: name 'x' is not defined
**********
```
>这就是作用域




## 作用域
- 定义函数其实就是定义了一个域，所有的函数内的定义都是在域内起作用，出了域就不起作用
- 所以函数内的定义的变量，只能是函数的范围之内使用，所以函数就是封装
- 也就是说，每一个变量都有作用域


### 作用域分类
#### 全局作用域
- 在整个程序运行环境都可见
- 全局作用域中的变量称为全局变量
#### 局部作用域
- 在函数、类内可见
- 局部作用域中的变量称为局部变量，其适用范围不能超过其所在的局部作用域


### 剖析
#### 例子1

```python
def outer():
    x = "A"
    print(x)
    def inner():
        x = "a"
        print(x)
    print(inner(),x)
print(outer())

结果如下
A
a
None A
None
```
- 局部变量和模块变量的关系是，内部不能向外突破，外部的变量可以向内突破
- 如果外部和内部都定义了这个变量，用内部的
- 赋值即定义

#### 例子2

```python
def fn():
    x += 100
    print(x)
print(x)
fn()
print(x)

报错了！
UnboundLocalError: local variable 'x' referenced before assignment
```
为什么会报错？
- 本地变量x在本地没有被赋值
- fn()内的x=是赋值语句，赋值即定义，就不能再引用全局变量，要使用本地变量
- 总结就是：在函数中，=就是赋值，被赋值就是调用本地变量

#### 例子3
```python
x = 100
def fn():
    print(x)
    x += 100
    print(x)
fn()
print(x)
```

结果是依旧报错的！
只要出现x = ，那么对于整个函数而言x就是局部变量！不看顺序

### global
在函数中声称某个变量为全局变量
当然，修改的也是全局变量
```python
x = 100
def fn():
    global x
    x += 100
    print(1,x)
    x += 200
    print(2,x)

print(3,x)
fn()
print(4,x)

结果如下
3 100
1 200
2 400
4 400
```

- global要慎用，毕竟全局变量是大家公用的，能不用就不用，简称：不用
- 使用global关键字的变量，将函数内的x声明为使用外部的全局作用域中定义的x
- 全局作用域中必须有x的的定义

看下下面这个例子
```python
def fn():
    global x
    x = 1
    x += 100
    print(1,x)
    x += 200
    print(2,x)

fn()
print(4,x)

结果如下
1 101
2 301
4 301
```
这是将内部变量穿透环境传送给了外部

### 传参导入全局变量
#### 不建议！
```python
x = []
def fn():
    x.append(1)
    return x
fn()
fn()
print(fn())
```
#### 建议
```python
x = []
def fn(x):
    x.append(1)
    return x
fn(x)
fn(x)
print(fn(x))
```
形参就是标识符，就是变量，就是本地变量！  
总结来看，内部函数使用全局变量最好使用传参的方式进去。不要在函数内使用global！




## 闭包(重要)
- 自由变量 -- 未在本地作用域中定义的变量，例如定义在内层函数外的外层函数的作用域的变量
- 闭包 -- 就是一个概念，出现在嵌套函数中，指的是内层函数引用到了外层函数的自由变量，就形成了闭包，很多语言都有这个概念，最熟悉的就是javascript


```python
def outer():
    a = [0]
    def inner():
        a[0] += 1
    return inner

m = outer()
m()
```
>这个不存在赋值即定义，因为没有改a，是改的a的某一个元素，所以不存在赋值即定义
这就是闭包的作用

### nonlocal
不是当前函数的局部变量，可以是外层函数的变量，但绝不是global

```python
def a():
    x = 0
    def b():
        nonlocal x
        x += 1
        return x
    return b
z = a()
z()
z()
print(z())
```

下面试一下缺省参数
```python
def acc(x = 1):
    x += 1
    print(x)
acc()
```


再看
```python
def acc(x = []):
    x.append(1)
    print(x)
acc()
acc()

结果如下
[1]
[1, 1]
```
>为什么第二次打印的结果是【1，1】？
- 因为函数也是对象，，每个函数被执行后，就生成了一个函数对象和函数名这个标识符关联
- python 把函数的默认值放在了函数对象的属性中，这个属性就伴随着这个函数对象的整个生命周期
- 查看函数_defaults_属性，它是个元组



```python
a = []
def acc(x = a):
    x.append(1)
    print(x)
acc()
print(acc.__defaults__)
acc()
print(acc.__defaults__)

结果如下
[1]
([1],)
[1, 1]
([1, 1],)
```



```python
def abc(x, y = 1, z ='abc'):
    x = 5

print(abc.__defaults__)

结果如下
(1, 'abc')
```
接着看
```python
def abc(x, y = 1, z ='abc'):
    y = 6
print(abc.__defaults__)
abc(x=111,z='sbb')
print(abc.__defaults__)
(1, 'abc')
(1, 'abc')
```


#### 练习
```python
def a(x = []):
    x = x + [5]

def b(x = []):
    x += [5]

print(a.__defaults__)
print(b.__defaults__)
a()
a()
b()
b()
print(a.__defaults__)
print(b.__defaults__)

结果如下
([],)
([],)
([],)
([5, 5],)
```

## 变量名解析原则LEGB(重点)
- Local --本地作用域，局部作用域的local命名空间，函数调用时创建，调用结束消亡
- Enclosing -- python2.2时引入了嵌套函数，实现了闭包，这个就是嵌套函数的外部函数的命名空间
- Global -- 全局作用域，即一个模块的命名空间，模块被import时创建，解释器退出时消亡
- Build-in -- 内置模块的命名空间，生命周期从python解释器启动时创建到解释器退出时消亡，例如print(open)，Print和open都是内置变量
所以一个名词的查找顺序就是LEGB




```python

```



```python

```
