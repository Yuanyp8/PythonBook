# 转置矩阵

## 题目1
有如下的矩阵，求其转置矩阵
```
1   2   3        1   4   7   
4   5   6  ==>>  2   5   8  
7   8   9        3   6   9
```
### 解题
规律：对角线不动，a[i][j] <=> a[j][i]
```python
matrix = [[1,2,3],[4,5,6],[7,8,9]]
print(*matrix,sep='\n')
for i,row in enumerate(matrix):
    for j,col in enumerate(row):
        if i > j:
            matrix[i][j],matrix[j][i] = matrix[j][i],matrix[i][j]
print(*matrix,sep='\n')

结果如下
[1, 2, 3]
[4, 5, 6]
[7, 8, 9]
[1, 4, 7]
[2, 5, 8]
[3, 6, 9]
```

## 题目2
有如下转置方阵
```
1   2   3
4   5   6
转换为如下矩阵
1   4
2   5
3   6
```
### 解题
#### 方法一
```python
matrix = [[1,2,3],[4,5,6]]
print(*matrix,sep='\n')
tm = [[None for _ in range(len(matrix))] for _ in range(len(matrix[0]))]
print(tm)
for i,row in enumerate(matrix):
    for j,col in enumerate(row):
        tm[j][i]=matrix[i][j]
print(*tm,sep='\n')

结果如下
[1, 2, 3]
[4, 5, 6]
[[None, None], [None, None], [None, None]]
[1, 4]
[2, 5]
[3, 6]
```

#### 方法二

```python
matrix = [[1,2,3],[4,5,6]]
print(*matrix,sep='\n')
tm = []
for i,row in enumerate(matrix):
    for j,col in enumerate(row):
        if not i:
            tm.append([])
        r = tm[j]
        r.append(col)
print(*tm,sep='\n')

结果如下
[1, 2, 3]
[4, 5, 6]
[1, 4]
[2, 5]
[3, 6]
```
对比append和先开辟，元素个数大的时候用一次性开辟
小的元素就是append



```python

```


```python

```




```python

```





```python

```



```python

```



```python

```



```python

```


```python

```
