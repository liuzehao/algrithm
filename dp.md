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
下面是李达的回溯解法：
```python
class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        res = []
        def func(st,temp):
            if temp ==temp[::-1]:
                res.append(len(temp))
            if len(temp)==len(s):
                return
            for i in range(len(st)):
                temp.append(st[i])
                func(st[i+1:],temp)
                temp.pop()
        func(s,[])
        return max(res)
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

### 2021.1.13 

叶丽丽：[474. 一和零](https://leetcode-cn.com/problems/ones-and-zeroes/)

```python
class Solution(object):
    def findMaxForm(self, strs, m, n):
        dp = [[[0] * (n + 1) for _ in range(m + 1)] for _ in range(len(strs) + 1)]
        for i in range(1, len(strs) + 1):
            ones = strs[i - 1].count("1")
            zeros = strs[i - 1].count("0")
            for j in range(m + 1):
                for k in range(n + 1):
                    dp[i][j][k] = dp[i - 1][j][k]
                    if j >= zeros and k >= ones and dp[i][j][k]<dp[i - 1][j - zeros][k - ones] + 1:
                        dp[i][j][k] = dp[i - 1][j - zeros][k - ones] + 1
        return dp[-1][-1][-1]
```

李达: [指 Offer 63. 股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        prices = [prices[i]-prices[i-1] for i in range(1,len(prices))]
        dp = 0
        res = 0
        for i in range(len(prices)):
            dp = max(dp+prices[i],0)
            if dp>res:
                res = dp
        return res
```


### 2021.1.14
刘泽豪：0-1背包
题目描述：有n个物品，它们有各自的体积和价值，现有给定容量的背包，如何让背包里装入的物品具有最大的价值总和？
输入参数：N和W分别是物体的数量和背包能装的重量。wt数组指的是物体的重量，val指的是对应的价值。

```python
def onezerobag(N,W,wt,val):
    dp=[[0 for i in range(W+1)] for i in range(N+1)]
    for i in range(1,N+1):
        for w in range(1,W+1):
            if w-wt[i-1]<0:
                dp[i][w]=dp[i-1][w]
            else:
                dp[i][w]=max(dp[i-1][w-wt[i-1]]+val[i-1],dp[i-1][w])
    return dp[N][W]
print(onezerobag(3,4,[2,1,3],[4,2,3]))
```
动规的第一步是建立一种状态并且遍历状态，这种想法有点类似于回溯模版的第一步，相当于遍历所有的可能性。但难点在于怎么确定状态所表示的意义。我理解这种状态应该具有一种“归一”性质，即当下的状态可以推导出下一状态，换句话说，当下的状态具有归纳之前状态的特性。
就本题而言，我们建立一个二维数组dp[i][w],状态的含义是到i这个数量为止当前容量下的最大价值。比如说dp[3][5]=6的意思就是在选择前3个物体容量控制在5的时候，最大价值是6。
第一步的难点在于如何定义dp的含义，目前有一种可能猜想是具有一个限制条件就二维,第一个for是个数的循环，第二个是限制条件的循环。如果两个限制条件就变成了三维，for循环依然第一层个数，第二第三层分别是限制条件。
第二步是遍历所有的可能性。
第三步是寻找状态转移。这个转移方程往往采用max这种形式。相当于回溯中的判断和递归函数的操作。第三步的难点在于找到对应的状态转移关系。
        
叶丽丽：[416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

```python
def fun(strs):
    n=sum(strs)
    if(n%2!=0):
        return False
    else:
        n/=2
    dp=[[False for _ in range(n+1)] for j in range(len(strs))]#n+1:还有背包容量为0的时候
    for i in range(len(strs)):
        dp[i][0] = True
    for i in range(len(strs)):
        for j in range(1,n+1):
            if(j-strs[i]<0):#装不下
                t=i-1
                dp[i][j]=dp[i-1][j]
            else:
                dp[i][j]=dp[i-1][j] or dp[i-1][j-strs[i]]#Amazing
    return dp[-1][-1]
```

李达: [1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

```python

```

### 2021.1.16