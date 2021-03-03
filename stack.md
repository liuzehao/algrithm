# 栈
栈在数据结构中的作用远比我过去以为的重要，主要可以分为四类问题。第一类问题是利用栈本身，第二类是辅助栈，第三类是双栈，最巧妙的应用还是第四类单调栈。
## 一、栈的特性
利用栈先入后出特性的题。在tree.md中我们介绍到递归是利用了系统栈进行分治操作，事实上我们可以用stack来自己写栈替换系统栈。所以我们会发现本节很多题会有两种解法，一种递归解法，一种利用栈来解题。这也验证了这两种写法的等价性，我们完全可以选择一种好理解的方式来掌握这种题。

[有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)
```python
class Solution:
    def isValid(self, s: str) -> bool:
        dic = {'{': '}',  '[': ']', '(': ')','?':'?'}
        stack = ['?']
        for c in s:
            if c in dic: stack.append(c)
            elif dic[stack.pop()] != c: return False 
        return len(stack) == 1
ss=Solution()
print(ss.isValid("]"))
```
本题要注意添加‘？’是用来防止程序报错的。stack.pop意味着stack不能是空的，如果是空的会报错。dit[]意味着dic中必须有'?'这个key，不然也会报错。整个程序的逻辑是只要字符串s中出现了非括号元素一定报错；当出现左括号就入栈，出现对应右括号就出栈。最后考虑边界问题:"?"是不是会导致出现bug的情况出现，事实上并不会，这是一个巧妙的设计。由于"?"的key和vaue相同，并不能进入出栈的代码，因此没有导致bug。


## 辅助栈
[最小栈](https://leetcode-cn.com/problems/min-stack/)
这个栈不仅仅记录value还记录索引
```python
class MinStack:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []

    def push(self, x: int) -> None:
        if not self.stack:
            self.stack.append([x,x])
        else:
            self.stack.append([x,min(self.stack[-1][1],x)])

    def pop(self) -> None:
        self.stack.pop()

    def top(self) -> int:
        return self.stack[-1][0]

    def getMin(self) -> int:
        return self.stack[-1][1]
```
[字符串解码](https://leetcode-cn.com/problems/decode-string/)
```python
class Solution:
    def decodeString(self, s: str) -> str:
        stack,multi,res=[],0,''
        for i in s:
            if i=='[':
                stack.append([multi,res])
                multi,res=0,''
            elif i==']':
                pre_multi,pre_res = stack.pop()
                res = pre_res+pre_multi*res
            elif '0'<=i<='9':
                multi= 10*multi+int(i)
            else:
                res+=i
        return res
```
## 单调栈
[739. 每日温度](https://leetcode-cn.com/problems/daily-temperatures/)[Li]
这道题构造了一个单调减的栈，去完成由于时序不一致造成的问题。
```python
class Solution:
    def dailyTemperatures(self, T) :
        res = [0 for _ in T]
        stack = []
        for i in range(len(T)):
            while stack and T[i]>T[stack[-1]]:
                topIndex = stack.pop()
                res[topIndex]=i-topIndex
            stack.append(i)
        return res
```
[84 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)[Ye]

本题用了单调栈，在左右添加0。当右边的柱子大于左边的柱子的时候入栈，当右边的柱子小于左边的柱子的时候出栈并计算面积。
```python
class Solution:
    def largestRectangleArea(self, heights) -> int:
        stack=[]
        heights=[0]+heights+[0]
        res=0
        for i in range(len(heights)):
            while stack and heights[stack[-1]] > heights[i]:
                tmp=stack.pop()
                res=max(res, (i - stack[-1] - 1) * heights[tmp])
            stack.append(i)
        return res
```

[85 最大矩形](https://leetcode-cn.com/problems/maximal-rectangle/)[Ye]
```python
class Solution:
    def maximalRectangle(self, matrix: List[List[str]]) -> int:
        if not matrix: return 0
        h,w=len(matrix),len(matrix[0])
        # 记录当前位置上方连续“1”的个数
        heights = [0] * (w + 2)
        res = 0
        for i in range(h):
            for j in range(1, w + 1):
                heights[j] = heights[j] + 1 if matrix[i][j - 1] == "1" else 0
            # 单调栈
            stack = []
            for k in range(len(heights)):
                while stack and heights[stack[-1]] > heights[k]:
                    tmp = stack.pop()
                    res = max(res, heights[tmp] * (k - stack[-1] - 1))
                stack.append(k)
        return res
```
[42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)[Li]
```python
#42.接雨水
class Solution:
    def trap(self, height: List[int]) -> int:
        stack,res = [],0
        for i in range(len(height)):
            while stack and height[i]>height[stack[-1]]:
                bottom = stack.pop()
                if not stack: break
                res += (min(height[stack[-1]],height[i])-height[bottom])*(i-stack[-1]-1)
            stack.append(i)
        return res
```

