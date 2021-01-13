# 二.动态规划
如果说回溯算法的模版套路还有迹可循，动态规划则要灵活的多。但近年来随着内卷程度的不断加深，动态规划越来越多的出现在了面试过程中。动态规划最显然的特征是，后面要求的值依赖于前面的值。核心是定义状态，和找到状态之间的转换方式。

## [例1:最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
抛开超时这个问题，其实这题也可以用回溯法来做，尝试一下？
最典型的dp莫过于最长上升子序列。
```python
def longstr(nums):
    dp=[1 for i in range(len(nums))]
    for t in range(len(nums)):
        for z in range(t):
            if nums[t]>nums[z]:
                dp[t]=max(dp[t],dp[z]+1)
    return max(dp)

print(longstr([10,9,25,37,101,18]))
```
```python 
#回溯解（超时）
def longstr(nums):
    seen=[]
    maxx=0
    def compute(llist):
        nonlocal maxx
        if len(llist)>1 and llist[-1]<llist[-2]:
            return
        maxx=max(maxx,len(llist))
        for i in range(len(nums)):
            if i in seen:
                continue
            seen.append(i)
            llist.append(nums[i])
            compute(llist)
            llist.pop()
        return maxx
    return compute([])
print(longstr([10,9,25,37,101,18]))
```
回溯和动态规划是一种什么样的关系？任何一种算法从本质上来讲就是枚举和选择，所以理论上任何算法都可以通过暴力枚举出来。动规之所以快在于消除了重叠子问题。所以，反过来说，任何一种算法只要含有重叠子问题并且可以通过上一状态推到下一状态，就可以使用动规来优化。

有一种错误观点：最值问题才用动态规划

[动态规划只需要求我们评估最优解是多少，最优解对应的具体解是什么并不要求。因此很适合应用于评估一个方案的效果](https://leetcode-cn.com/problems/permutations/solution/quan-pai-lie-hui-su-suan-fa-by-cherry-n1/)

## [例2 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)
与上一题不一样，这题是二维动规。同时，我们将根据这题来学习状态压缩的方法。关于这题的具体分析可以参看：
[这题的题解](https://leetcode-cn.com/problems/longest-palindromic-subsequence/solution/516-zui-chang-hui-wen-zi-xu-lie-by-ming-zhi-shan-y/)
```python
def longeststr(ss):
    ls=len(ss)
    dp=[[0 for i in range(ls)] for t in range(ls)]
    for z in range(ls):
        dp[z][z]=1
    for i in range(ls-1,-1,-1):
        for j in range(i+1,ls):
            if ss[i]==ss[j]:
                dp[i][j]= dp[i+1][j-1]+2
            else:
                dp[i][j]=max(dp[i+1][j],dp[i][j-1])
    return dp[0][ls-1]

print(longeststr("bbbab"))
```

下面主要来研究一下本体的状态压缩优化：
```python
    def longeststr(ss):
        ls=len(ss)
        dp=[1 for i in range(ls)]
        for i in range(ls-2,-1,-1):
            pre=0
            for j in range(i+1,ls):
                temp=dp[j]
                if ss[i]==ss[j]:
                    dp[j]=pre+2
                else:
                    dp[j]=max(dp[j],dp[j-1])
                pre=temp
        return dp[ls-1]
    print(longeststr("bbbab"))
```

## 题整合

[俄罗斯套娃信封问题](https://leetcode-cn.com/problems/russian-doll-envelopes/)

最先想到的解法，先把第一列排序一下，然后后一列最长子序列解一下。这个解法超时了，需要优化
```python
class Solution:
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        if not envelopes:
            return 0
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        dp=[1 for i in range(len(envelopes))]

        for t in range(len(envelopes)):
            for z in range(t):
                if envelopes[t][1]>envelopes[z][1] and envelopes[t][0]>envelopes[z][0]:
                    dp[t]=max(dp[z]+1,dp[t])
        return max(dp)
```

全排列-动态规划解法
```python
class Solution:
    def permute(self, nums):
        if not nums:
            return None
        dp = [[] for i in range(len(nums))]
        dp[0] = [[nums[0]]]
        for i in range(1, len(nums)):
            for ans in dp[i-1]:
                dp[i].append(ans+[nums[i]])
                for j in range(len(ans)):
                    dp[i].append(ans[:j]+[nums[i]]+ans[j+1:]+[ans[j]])
        return dp[-1]

ss=Solution()
print(ss.permute([1,2,3,4]))
```
最大子数组

这题是中科院博士笔试的最后一题，典型的动态规划。相当于求一个包含正负数数组的最大子数组。跟最长子上升子序列很像。

![最大子序列](./pic/longsonstr.png)
核心代码就这样：
```python
def longzistr(nums):
    dp=nums.copy()
    for t in range(len(nums)):
        dp[t]=max(nums[t],nums[t]+dp[t-1])
    return max(dp)
nums=[-10,10,-5,2,3,100,-50]
print(longzistr(nums))
print(longzistr(nums))
```
修改以符合题意：
```python
C=[10,10,5,2,3]
S=[-1,1,-1,1,1]
def compute(C,S):
    nums = list(map(lambda x,y:x*y,C,S))
    dp=nums.copy()
    for t in range(len(nums)):
        dp[t]=max(nums[t],nums[t]+dp[t-1])
    return max(dp)
print(compute(C,S))
``` 
进一步，我们发现dp[i]的值只和dp[i-1]相关。也就是说：
[-10,10,-5,2,3]
这个数组的dp从上一种做法的dp为:
[-10,10,5,12,15]
还可以看作:
[-10,10,15,12,15]
```python
C=[10,10,5,2,3]
S=[-1,1,-1,1,1]
def compute(C,S):
    nums = list(map(lambda x,y:x*y,C,S))
    dp0=nums[0]
    temp=0
    res=0
    for t in range(1,len(nums)):
        temp=max(nums[t],nums[t]+dp0)
        dp0=temp
        res=max(res,temp)
    return res
print(compute(C,S))
```