# 二.动态规划
如果说回溯算法的模版套路还有迹可循，动态规划则要灵活的多。但近年来随着内卷程度的不断加深，动态规划越来越多的出现在了面试过程中。动态规划最显然的特征是，后面要求的值依赖于前面的值。核心是定义状态，和找到状态之间的转换方式。

## 例1:最长递增子序列
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
## 题整合
[俄罗斯套娃信封问题](https://leetcode-cn.com/problems/russian-doll-envelopes/)
#这个解法超时了，需要优化
```python
class Solution:
    def maxEnvelopes(self, envelopes):
        if not envelopes:
            return 0
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        dp=[1 for i in range(len(envelopes))]

        for t in range(len(envelopes)):
            for z in range(t):
                if envelopes[t][1]>envelopes[z][1] and envelopes[t][0]>envelopes[z][0]:
                    dp[t]=max(dp[z]+1,dp[t])
        return max(dp)

ss=Solution()
print(ss.maxEnvelopes([[4,5],[4,6],[6,7],[2,3],[1,1]]))
```