# 双指针问题

![twopoint](./pic/twopoint.png)

双指针问题分为三类，快慢指针，左右指针，子串问题。其中左右指针是子问题较多的，三个大类，一是二分查找及衍生，二是滑动窗口，三是快速排序

## 一.左右指针
左右指针在双指针问题中最为常见，并且很灵活。

### 1.1 二分查找
不同于排序算法有多种，查找算法一般就是二分查找，二叉树查找（二叉树的遍历）。如果把更宽泛意义上的，比如查找字符串算上，那就有回溯算法，滑窗这两个。如果说要求log复杂度，一般就是二分没跑了。

1.1.1 二分的原始写法
二分问题最大的难点在于边界问题
```python
def leftbinaysearch(nums,target):
        left=0
        right=len(nums)-1
        while left<=right: #注意点一：<= 原因是[0] 0 如果不是==就直接返回-1了
                mid=left+(right-left)//2
                if nums[mid]<target:
                        left=mid+1
                elif nums[mid]>target:
                        right=mid-1
                elif nums[mid]==target:#注意点二：三个判断条件都是互斥的
                         return mid
        return -1
num=[1,2,3,9,10,45]
print(binaySearch(num,45))
```

1.1.2 寻找左右边界
考虑左右边界会比较绕，if 条件有两个要注意。
```python
def leftbinaysearch(nums,target):
        left=0
        right=len(nums)-1
        while left<=right:
                mid=left+(right-left)//2
                if nums[mid]<target:
                        left=mid+1
                elif nums[mid]>target:
                        right=mid-1
                elif nums[mid]==target:
                        left=mid+1
        # if left>=len(nums) or nums[left]!=target:
        #         return -1
        if right<0 or nums[right]!=target:
                return -1
        return right

def leftbinaysearch(nums,target):
        left=0
        right=len(nums)-1
        while left<=right:
                mid=left+(right-left)//2
                if nums[mid]<target:
                        left=mid+1
                elif nums[mid]>target:
                        right=mid-1
                elif nums[mid]==target:
                        right=mid-1
        if left>=len(nums) or nums[left]!=target:
                return -1
        return left
```
1.1.3 衍生题
二分查找的通用性导致基于这种题的衍生题特别的多
[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

这是一个二分查找的问题，二分查找最需要注意的是边界问题。既可以通过上面左右边界改，也可以直接找到之后进行左右嗅探。
```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        left=0
        right=len(nums)-1
        while left<=right:
            mid=left+(right-left)//2
            if nums[mid]<target:
                left=mid+1
            elif nums[mid]>target:
                right=mid-1
            elif nums[mid]==target:
                res=[mid,mid]
                temp=mid-1
                while temp>=0 and nums[temp]==target:
                    res[0]=temp
                    temp-=1
                temp=mid+1
                while temp<len(nums) and nums[temp]==target:
                    res[1]=temp
                    temp+=1
                return res
        return [-1,-1]
```
比如 left<=right，这个地方必须是<=，比如[1],1这个案例如果<就不能进入循环了。同时本题也可以采用左右边界写法，那样子写的话要分别实现左右边界函数。

[33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
一个比较绕二分查找的题目，要注意边界条件。同时注意题目中说了不含重复元素，个人感觉这是一个不是很必要的的条件。
```python
class Solution:
    def search(self, nums, target: int) -> int:
        left=0
        right=len(nums)-1
        while left<=right:
            mid=left+(right-left)//2
            #判断左边一半是有序的
            if nums[mid]==target:
                return mid
            if nums[mid]>=nums[0]:#这里等号[1,1,1,1,1,3]
                if nums[0]<=target<nums[mid]:
                    right=mid-1
                else:
                    left=mid+1
            #右边部分是有序的
            else:
                if nums[mid]<target<=nums[len(nums)-1]:#【1，3】1
                    left=mid+1
                else:
                    right=mid-1
        return -1
ss=Solution()
print(ss.search([3,1],1))
```
[4. 寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
这是一个很难理解的题目，可以说是二分问题中最难让人理解的了。
```python
```