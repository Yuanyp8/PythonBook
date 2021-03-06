## 简单选择排序
- 属于选择排序
- 两两比较大小，找出极值，被放置在固定位置，这个固定位置为某一端
- 结果分为升序和降序

### 降序
n个数从左至右，索引从0到n-1，两两依次比较，记录极大值的索引，这一轮比较完毕将极大值和边界的值交换，如果大数就是索引1那么就不用交换；第二轮依次比较，找到极大值和边界交换


### 升序
和降序相反，原理一样


## 代码
按照降序排序
```python
import random


# 得到一个长度为10的随机列表
lst = [random.randint(1,100) for _ in range(10)]
print("Pre List: >>>>",lst)
length = len(lst)
for i in range(length):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    for j in range(max_num+1,length): # 列表的左边会按照第一层的循环开始固定
        if lst[j] > lst[max_num]:
            max_num = j
    if max_num != i: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换

print("Cur List: >>>>",lst)


结果如下
Pre List: >>>> [41, 20, 1, 12, 6, 82, 44, 6, 70, 68]
Cur List: >>>> [82, 70, 68, 44, 41, 20, 12, 6, 6, 1]

```



## 二元选择排序
在上面的基础上，同时求最小值

```python
import random


# 得到一个长度为10的随机列表
lst = [random.randint(1,100) for _ in range(10)]
print("Pre List: >>>>",lst)
length = len(lst)

for i in range(length):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    min_num = -1-i # 假设最小值为负索引开始并向右移动
    for j in range(max_num+1,length-i): # 列表的左边会按照第一层的循环开始固定,右边也缩小
        if lst[j] > lst[max_num]:
            max_num = j
        if lst[-1-j] < lst[min_num]:
            min_num = -1-j
    else:
        min_num = length + min_num   # 负转正
    if max_num != i: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换
        if min_num == i:
            min_num = max_num
    if min_num != -i-1:
        lst[min_num],lst[-1-i] = lst[-1-i],lst[min_num]
print("Cur List: >>>>",lst)

结果如下
Pre List: >>>> [61, 6, 85, 85, 32, 26, 87, 78, 72, 94]
Cur List: >>>> [94, 87, 85, 85, 78, 72, 61, 32, 26, 6]
```


### 继续优化，减少循环次数


先看下这样循环了多少次
```python
import random


# 得到一个长度为10的随机列表
lst = [random.randint(1,100) for _ in range(10)]
print("Pre List: >>>>",lst)
length = len(lst)
count_iter = 0
count_swap = 0
for i in range(length):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    min_num = -1-i # 假设最小值为负索引开始并向右移动
    for j in range(max_num+1,length-i): # 列表的左边会按照第一层的循环开始固定,右边也缩小
        count_iter += 1
        if lst[j] > lst[max_num]:
            max_num = j
        if lst[-1-j] < lst[min_num]:
            min_num = -1-j
    else:
        min_num = length + min_num
    if max_num != i: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换
        count_swap += 1
        if min_num == i:
            min_num = max_num
    if min_num != -i-1:
        lst[min_num],lst[-1-i] = lst[-1-i],lst[min_num]
        count_swap += 1
print("Cur List: >>>>",lst)
print(count_iter,count_swap)

结果如下
Pre List: >>>> [84, 79, 97, 13, 90, 12, 67, 67, 90, 99]
Cur List: >>>> [99, 97, 90, 90, 84, 79, 67, 67, 13, 12]
25 15
```



继续利用这个列表，减少次数

```python
lst  = [84, 79, 97, 13, 90, 12, 67, 67, 90, 99]
print("Pre List: >>>>",lst)
length = len(lst)
count_iter = 0
count_swap = 0
for i in range(length//2):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    min_num = -1-i # 假设最小值为负索引开始并向右移动
    for j in range(max_num+1,length-i): # 列表的左边会按照第一层的循环开始固定,右边也缩小
        count_iter += 1
        if lst[j] > lst[max_num]:
            max_num = j
        if lst[-1-j] < lst[min_num]:
            min_num = -1-j
    else:
        min_num = length + min_num
    if max_num != i: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换
        count_swap += 1
        if min_num == i:
            min_num = max_num
    if min_num != -i-1:
        lst[min_num],lst[-1-i] = lst[-1-i],lst[min_num]
        count_swap += 1
print("Cur List: >>>>",lst)
print(count_iter,count_swap)

结果如下
Pre List: >>>> [84, 79, 97, 13, 90, 12, 67, 67, 90, 99]
Cur List: >>>> [99, 97, 90, 90, 84, 79, 67, 67, 13, 12]
25 10
```


排除所比较的元素相等情况
```python
lst  = [84, 79, 97, 13, 90, 12, 67, 67, 90, 99]
print("Pre List: >>>>",lst)
length = len(lst)
count_iter = 0
count_swap = 0
for i in range(length//2):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    min_num = -1-i # 假设最小值为负索引开始并向右移动
    for j in range(max_num+1,length-i): # 列表的左边会按照第一层的循环开始固定,右边也缩小
        count_iter += 1
        if lst[j] > lst[max_num]:
            max_num = j
        if lst[-1-j] < lst[min_num]:
            min_num = -1-j
    else:
        min_num = length + min_num
    if max_num != i: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换
        count_swap += 1
        if min_num == i:
            min_num = max_num
    if min_num != -i-1:
        lst[min_num],lst[-1-i] = lst[-1-i],lst[min_num]
        count_swap += 1
    if lst[max_num] == lst[min_num]:
        break
print("Cur List: >>>>",lst)
print(count_iter,count_swap)
```

继续优化，值相同不用交换
```python
lst  = [84, 79, 97, 13, 90, 12, 67, 67, 90, 99]
print("Pre List: >>>>",lst)
length = len(lst)
count_iter = 0
count_swap = 0
for i in range(length//2):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    min_num = -1-i # 假设最小值为负索引开始并向右移动
    for j in range(max_num+1,length-i): # 列表的左边会按照第一层的循环开始固定,右边也缩小
        count_iter += 1
        if lst[j] > lst[max_num]:
            max_num = j
        if lst[-1-j] < lst[min_num]:
            min_num = -1-j
    else:
        min_num = length + min_num
    if max_num != i and lst[min_num] != lst[i]: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换
        count_swap += 1
        if min_num == i:
            min_num = max_num
    if min_num != -i-1:
        lst[min_num],lst[-1-i] = lst[-1-i],lst[min_num]
        count_swap += 1
    if lst[max_num] == lst[min_num]:
        break
print("Cur List: >>>>",lst)
print(count_iter,count_swap)

结果如下
Pre List: >>>> [84, 79, 97, 13, 90, 12, 67, 67, 90, 99]
Cur List: >>>> [99, 97, 90, 90, 84, 79, 67, 67, 13, 12]
25 9
```



### 最终版
```python
import random


lst  = [random.randint(1,100) for _ in range(10)]
print("Pre List: >>>>",lst)
length = len(lst)
for i in range(length//2):
    max_num = i   # 假设最大值的索引为0，并依次往右移动
    min_num = -1-i # 假设最小值为负索引开始并向右移动
    for j in range(max_num+1,length-i): # 列表的左边会按照第一层的循环开始固定,右边也缩小
        if lst[j] > lst[max_num]:
            max_num = j
        if lst[-1-j] < lst[min_num]:
            min_num = -1-j
    else:
        min_num = length + min_num
    if max_num != i and lst[min_num] != lst[i]: # 减少交换次数，如果位置不需要变化
        lst[max_num],lst[i] = lst[i],lst[max_num] # 交换
        if min_num == i:
            min_num = max_num
    if min_num != -i-1:
        lst[min_num],lst[-1-i] = lst[-1-i],lst[min_num]
    if lst[max_num] == lst[min_num]:
        break
print("Cur List: >>>>",lst)
```
