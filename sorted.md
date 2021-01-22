# 排序
## 快排：
快排思路其实很简单。
1.利用partition函数在数组中找到一个mid下标，通过移动元素，使得mid左边的元素都比mid小，右边的都比mid大。

2.递归对mid左边和右边的元素进行快速排序

3.不断进行下去，区间会越来越小，函数如果start==end说明区间只有一个元素，也就不用排序，这就是终止条件。

快排其实是有很多版本的，最开始的是霍尔的版本，是一个递归的版本。下面代码基于霍尔版本，在patition的问题上做了点改进，可以算比较纯正的快排了，如果在patition的选择上加入随机，就是随机快排，理论上这种快排速度最快。

快速排序的副产品就是快速选择算法，因为partition函数实际上返回的mid值就是array[mid]在已经排序的array里面所处的顺位，所以我们在学习快速排序的时候可以顺便把Top k也学了。
```python
def quick_sort(lists,i,j):#i左，j右
    def partition(left,right,pivot):
        pivott=lists[pivot]#端点
        lists[pivot],lists[right]=lists[right],lists[pivot]#便端点到最右边
        index=left
        for i in range(left,right):
            if lists[i]>pivott:
                lists[i],lists[index]=lists[index],lists[i]
                index+=1
        lists[right],lists[index]=lists[index],lists[right]
    if i >= j:
        return lists
    pivot = i
    low = i
    high = j
    partition(i,j,pivot)
    quick_sort(lists,low,i-1)
    quick_sort(lists,i+1,high)
    return lists
lists=[62,35,23,54,48,10]
print(quick_sort(lists,0,len(lists)-1))
```

我们只要添加两行代码就可以把快排改成快速选择算法，我还没改完，其实就是一个return 的问题，学过回溯图之后想想这个应该怎么改比较好？
```python
def quick_sort(lists,i,j):#i左，j右
    def partition(left,right,pivot):
        pivott=lists[pivot]#端点
        lists[pivot],lists[right]=lists[right],lists[pivot]#便端点到最右边
        index=left
        for i in range(left,right):
            if lists[i]>pivott:
                lists[i],lists[index]=lists[index],lists[i]
                index+=1
        lists[right],lists[index]=lists[index],lists[right]
        return index#1.返回index
    if i >= j:
        return lists
    pivot = i
    low = i
    high = j
    index=partition(i,j,pivot)#2.这一行是index
    if index==2:#3.index就是top k
        print(lists[index])
    quick_sort(lists,low,i-1)
    quick_sort(lists,i+1,high)
    return lists
lists=[62,35,23,54,48,10]
print(quick_sort(lists,0,len(lists)-1))
```
当然我们知道，一切递归算法都可以用模拟栈的方法来改成非递归代码，下面是改进的非递归快排序：
```python
def quick_sort(arr):
    if len(arr) < 2:
        return arr
    stack = []
    stack.append(len(arr)-1)
    stack.append(0)
    while stack:
        l = stack.pop()
        r = stack.pop()
        index = partition(arr, l, r)
        if l < index - 1:
            stack.append(index - 1)
            stack.append(l)
        if r > index + 1:
            stack.append(r)
            stack.append(index + 1)
def partition(arr, start, end):
    # 分区操作，返回基准线下标
    pivot = arr[start]
    while start < end:
        while start < end and arr[end] >= pivot:
            end -= 1
        arr[start] = arr[end]
        while start < end and arr[start] <= pivot:
            start += 1
        arr[end] = arr[start]
    # 此时start = end
    arr[start] = pivot
    return start
 
if __name__ == '__main__':
    list = [6, 12, 27, 34, 21, 4, 9, 8, 11, 54, 39, 7, 3]
    quick_sort(list)
    print(list)

```
当然我们也可以不实现找partition这个函数，直接两块合到一起，问题是就不能顺便求patition了。优点是代码简单多了，思想还是一样的。
```python
def quick_sort(lists,i,j):#i左，j右
    if i >= j:
        return lists
    pivot = lists[i]
    low = i
    high = j
    while i < j:
        while i < j and lists[j] >= pivot:
            j -= 1
        lists[i]=lists[j]
        while i < j and lists[i] <=pivot:
            i += 1
        lists[j]=lists[i]
    lists[j] = pivot
    quick_sort(lists,low,i-1)
    quick_sort(lists,i+1,high)
    return lists

if __name__=="__main__":
    lists=[2,4,6,8,3,11,20,15]
    print("排序前的序列为：")
    for i in lists:
        print(i,end =" ")
    print("\n排序后的序列为：")
    for i in quick_sort(lists,0,len(lists)-1):
        print(i,end=" ")
```

## 堆排：
```python
def heapify(arr, n, i): 
    largest = i  
    l = 2 * i + 1     # left = 2*i + 1 
    r = 2 * i + 2     # right = 2*i + 2 
  
    if l < n and arr[i] < arr[l]: 
        largest = l 
  
    if r < n and arr[largest] < arr[r]: 
        largest = r 
  
    if largest != i: 
        arr[i],arr[largest] = arr[largest],arr[i]  # 交换
  
        heapify(arr, n, largest) 
  
def heapSort(arr): 
    n = len(arr) 
  
    # 从下到上
    for i in range(n, -1, -1): 
        heapify(arr, n, i) 
  
    # 从上到下
    for i in range(n-1, 0, -1): 
        arr[i], arr[0] = arr[0], arr[i]   # 交换
        heapify(arr, i, 0) 
  
arr = [ 12, 11, 13, 5, 6, 7] 
heapSort(arr) 
print(arr)
```

堆topk:

```python
def heapify(arr, n, i): 
    largest = i  
    l = 2 * i + 1     # left = 2*i + 1 
    r = 2 * i + 2     # right = 2*i + 2 
    if l < n and arr[i] < arr[l]: 
        largest = l 
    if r < n and arr[largest] < arr[r]: 
        largest = r 
    if largest != i: 
        arr[i],arr[largest] = arr[largest],arr[i]  # 交换
        heapify(arr, n, largest) 
  
def heapSort(arr,k): 
    n = len(arr) 

    # 从下到上
    for i in range(n, -1, -1): 
        heapify(arr, n, i) 
  
    # 从上到下
    for i in range(n-1, 0,-1): 
        arr[i], arr[0] = arr[0], arr[i]   # 交换
        heapify(arr, i, 0)
        if i==k:
            return arr[k-1]
            
arr = [ 12, 11, 13, 5, 6, 7]
k=2
print(heapSort(arr,k))
```