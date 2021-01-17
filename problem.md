## 回溯算法
### 1.如果无法理清楚回溯算法的逻辑：
尝试画出回溯图

### 2.如果经常在回溯中分不清递归结构和循环结构：
尝试用stack顺序结构改写回溯结构以厘清这两种结构之间的差别

### 3.glabel,nonlocal什么区别和用法：
待

### 4.or的用法以及应用的场景：
从左到右运行顺序，遇到函数返回为False时，继续运行OR之后的函数，类似枚举，当全部函数为False时OR表达式整体return值为False；当函数返回True时，不再运行OR之后的函数，类似剪枝操作，此时OR表达式整体return值为True。

### 5.python中llist左闭右开
```python
llist=[1,2,3,4]
print(llist[4:])#输出[]
print(llist[4])#输出index erro
```

## 动态规划
### 1.python如何进行排序
sort、sorted
常见的lambda表达式：
student = [['Tom', 'A', 20], ['Jack', 'C', 18], ['Andy', 'B', 11]]
student.sort(key=lambda student: student[2])

### 2.map的用法

### 3.lambda表达式

### 4.python中的二维数组[][]是行列排列的

### 5.（猜想）什么时候以步长作为循环条件？猜想：子串步长，子序列for？目标两题回溯拆分都是子序列问题，符合猜想。


### 6.（猜想）背包问题中定义状态方式显然不止一种，不同的定义方式有什么关联？