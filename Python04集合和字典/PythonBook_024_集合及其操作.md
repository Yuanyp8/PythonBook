# 集合
- `set`翻译为集合
- `collection`翻译为集合类型，是一个大概念
- `set`是可变的、**无序的**、不重复的元素的集合

## 集合的定义
利用set函数构建空集合
```python
>>> s1 = set()
>>> type(s1)
<class 'set'>

>>> s3 = {1,2,3}
>>> type(s3)
<class 'set'>

>>> s5 = set(range(5))
>>> s5
{0, 1, 2, 3, 4}
>>> type(s5)
<class 'set'>
```

## 集合的性质
- set的元素要求必须可以hash(线性数据结构的元素必须不可以hash)
- 目前接触的不可hash的类型有set和list
- 元素不可以使用索引
- set可以迭代

>无序不是真的无序，只不过hash作为序列

## set操作

### 增加元素

```python
>>> s = set()
>>> s.add(1)
>>> s
{1}
>>> type(s)
<class 'set'>
```

### update
注意返回值为None,类似于列表的extend，但是遇到重复元素会去重
```python
>>> s
{1, 2}
>>> s1
set()
>>> s1.update(s)
>>> s1
{1, 2}
```

### remove
- 从remove移除一个元素  
- 注意，删除没有的元素会报错（KeyError）

```python
>>> s1
{1, 2}
>>> s1.remove(1)
>>> s1
{2}
```

### discard

discard()不会抛异常
```python
>>> s1
{2}
>>> s1.discard(3)
>>> s1
{2}
```


### pop
pop()会随机pop一个函数出来
```python
>>> s
{2, 3, 4, 5}
>>> s.pop()
2
>>> s
{3, 4, 5}
```


### 修改
set不支持修改，要么删除元素，要么加入元素

### 查询
非线性结构，无法利用索引

### 遍历
可以迭代所有元素

### 成员运算符
`in`、`not in` 能够判断元素是否在set中

## set和线性结构
- 线性结构可以利用索引，查询某个元素在不在其中需要遍历，所以时间复杂度为O(n),会随着数据量增大而增加耗时
- set dict等结构，内部使用hash作为key，时间复杂度可以做到O(1),查询速度和数据量大小无关

### 可hash
- 数值型：`int`、 `float`、 `complex`
- 布尔型：`True`、`False`
- 字符串String和Bytes
- touple
- None
>以上都是不可变类型，是可hash类型，hashable


### hash
Hash，一般翻译做散列、杂凑，或音译为哈希，是把任意长度的输入（又叫做预映射pre-image）通过散列算法变换成固定长度的输出，该输出就是散列值。这种转换是一种压缩映射，也就是，散列值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出，所以不可能从散列值来确定唯一的输入值。简单的说就是一种将任意长度的消息压缩到某一固定长度的消息摘要的函数。

- 什么是摘要算法呢？摘要算法又称哈希算法、散列算法。它通过一个函数，把任意长度的数据转换为一个长度固定的数据串（通常用16进制的字符串表示）
- Hash算法可以将一个数据转换为一个标志，这个标志和源数据的每一个字节都有十分紧密的关系。Hash算法还具有一个特点，就是很难找到逆向规律。
- Hash算法是一个广义的算法，也可以认为是一种思想，使用Hash算法可以提高存储空间的利用率，可以提高数据的查询效率，也可以做数字签名来保障数据传递的安全性。所以Hash算法被广泛地应用在互联网应用中
- Hash算法也被称为散列算法，Hash算法虽然被称为算法，但实际上它更像是一种思想。Hash算法没有一个固定的公式，只要符合散列思想的算法都可以被称为是Hash算法。

## set的效率分许

### remove
由于是利用的hash算法，删除一个元素不需要遍历，直接利用hash表去相应的hash空间（内存地址）删除元素即可  
时间复杂度为O(1)
### pop
与pop类似，不查询hash表，直接去内存地址随机删除一个元素  
时间复杂度为O(1)
### in 和 not in
查询hash表直接寻址判断内存空间是否有元素  
时间负载度为O(1)

### 遍历
遍历和元素个数有关，时间复杂度为O(n)

## 集合

### 全集
所有元素的集合，例如实数集合，所有实数组成的集合就是全集

![](http://img5.imgtn.bdimg.com/it/u=1206858782,630100923&fm=26&gp=0.jpg)
>图中最外层可称为其他集合的全集

### 子集`subset`和超集`superset`
一个集合A的所有元素都在集合B里面，A为B的子集，B为A的超集

![](http://img-blog.csdnimg.cn/20190321213848446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMzIyMTAz,size_16,color_FFFFFF,t_70)


### 真子集和真超集
A为B的子集，且A不等于B，A就是B的真子集，B为A的真超集


### 并集
多个集合合并的结果
![](http://a.hiphotos.baidu.com/zhidao/pic/item/9345d688d43f8794644c74cfd51b0ef41ad53a9b.jpg)



### 交集
多个集合的公共部分

### 差集
集合中去除和其他集合的公共部分
![](http://gss2.bdstatic.com/-fo3dSag_xI4khGkpoWK1HF6hhy/baike/s=220/sign=0904009441a7d933bba8e3719d4ad194/86d6277f9e2f07082e8f1080ea24b899a801f2a3.jpg)


### 操作

#### 交集
```python
>>> a
{1, 2, 3}
>>> b
{3, 4, 5}
>>> a&b
{3}
```

#### 并集
```python
>>> a|b
{1, 2, 3, 4, 5}
```


#### 差集
```python
>>> a,b
({1, 2, 3}, {3, 4, 5})
>>> a-b
{1, 2}
>>> b-a
{4, 5}
```

#### 对称差集

```python
>>> a,b
({1, 2, 3}, {3, 4, 5})
>>> a^b
{1, 2, 4, 5}
```
#### 判断子集

```python
>>> a,b,c
({1, 2, 3}, {3, 4, 5}, {3})
>>> a <= b
False
>>> c <= a
True
```

#### 判断真子集
```python
>>> a,b,c
({1, 2, 3}, {3, 4, 5}, {3})
>>> c < a
True
>>> a < c
False
```

#### 判断超集
```python
>>> a,b,c
({1, 2, 3}, {3, 4, 5}, {3})
>>> a >= b
False
>>> a >= c
True
```


#### 判断真超集
```python
>>> a,b,c
({1, 2, 3}, {3, 4, 5}, {3})
>>> a>c
True
```


### 练习

#### 共同好友
你的好友A、B、C，他的好友C、B、D，求共同好友

```python
#使用交集就可以
{"a","b"} & {"b","c"}
{'b'}
```




#### 微信群提醒
XXX与群里其他人都不是微信朋友关系

```python
#同样用交集即可。新入群的所有朋友为一个集合，群里的所有人为一个集合
#两个集合没有交集即可证明
>>> {1,2,3,4,5} & {6,7,8,9,0}
set()
```



#### 权限判断
- 有一个api，要求权限同时具备A、B、C才能访问，用户权限是B、C、D，判断用户是否能访问api

```python
#用子集判断即可
>>> {1,2,3} <= {1,2,3,4,5}
True
```













- 有一个api，要求权限具备A、B、C任意一项就能访问，用户权限是B、C、D，判断用户是否能访问api


```python
#交集即可
>>> {1,2,3} & {2,4,5}
{2}
```



#### 任务列表
一个总任务列表，存储所有任务，一个完成的任务列表，找出未完成的任务列表

```python

```
