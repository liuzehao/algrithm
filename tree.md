# 树
树的题主要是由两部分组成，其一是结构本身所引起。其中又包含两种方式，一种是计算节点。一种是根据遍历方式所引起的题目。其二是从算法衍生而来，主要是搜索，递归树。
## 一.根据遍历所引起的题目
## [297. 二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)
在面试过程中二叉树，我们往往需要先手写建立二叉树，然后才能验证我们写的算法正确性。所以学会二叉树的建立是很重要的，二叉树的遍历分为深度优先遍历（DFS）和广度优先遍历。通过二叉树的序列化和反序列化，我们可以学习典型的二叉树建立方式。
下面我们采用DFS的三种方式来序列化和反序列化二叉树。
1.1 广度优先遍历：在这个说明问题之前，我们要先注意两个问题
问题一：leetcode通常采用层序遍历来表达二叉树，字符串和二叉树结构的对应关系与官方文档上并不一致
[二叉树测试用例意义官方解释](https://support.leetcode-cn.com/hc/kb/article/1194353/)
事实上输入用例是遍历到最后一个None的位置的，比如：
```
      5
     / \
    4   7
   /   /
  3   2
 /   /
-1  9
```
对应的list应该是：[5,4,7,3,null,2,null,-1,null,9,null,null,null,null,null]也就是：
```
         5
        / \
       4   7
      /   /
     3   2
    /   / 
   -1  9 ----中间也有null不写出来了
  / \ / \
null null null null-----到这层为止
```

问题二：在序列化的过程中输入的是一个树的root节点，输出的是一个字符串。在反序列过程中，输入的是字符串输出的是root节点。


明确这两个问题之后，我们先进行广度优先遍历序列化操作。我们知道广度遍历要用到队列，所以只有非递归的写法。
```python
#显然序列化操作本质上就是一个采用quene的广度优先遍历操作，区别是我们需要把null也记录下来，记录下来后的结构依照问题一。并注意最后转化成字符串。
    def serialize(self, root):
        if not root: return "[]"
        queue = []
        queue.append(root)
        res = []
        while queue:
            node = queue.pop(0)
            if node:
                res.append(str(node.val))
                queue.append(node.left)
                queue.append(node.right)
            else: res.append("null")
        return '[' + ','.join(res) + ']'
      
```
反序列化：
反序列化也利用了队列的结构。
```python
    def deserialize(self,data):
        if data=='[]':
            return None
        vals, i = data[1:-1].split(','), 1
        root = TreeNode(int(vals[0]))
        queue = []
        queue.append(root)
        while queue:
            node = queue.pop(0)
            if vals[i] != "null":
                node.left = TreeNode(int(vals[i]))
                queue.append(node.left)
            i += 1
            if vals[i] != "null":
                node.right = TreeNode(int(vals[i]))
                queue.append(node.right)
            i += 1
        return root
```
同时我们要注意，只有完全二叉树具有反序列化的递归写法。想想是为什么？其实完全二叉树具有递归的形式，而普通二叉树并不是递归的。

1.2 深度优先遍历



## 二叉树的建立
```python
class TreeNode:
    def __init__(self,x):
        self.val=x
        self.left=None
        self.right=None
    #打印树
class TreeNodeTools:
    def printf(self,root):
        print(root.val)
        if root.right:
            self.printf(root.right)
        if root.left:
            self.printf(root.left)
    #行序遍历建树
    def createTreeByrow(self,llist,i):
        if llist[i]=='null':
            return
        root=TreeNode(llist[i])
        if i*2+1<len(llist):
            root.left=self.createTreeByrow(llist,i*2+1)
        if i*2+2<len(llist):
            root.right=self.createTreeByrow(llist,i*2+2)
        return root
if __name__ == "__main__":
    ss=TreeNodeTools()
    root3=ss.createTreeByrow([5,4,8,11,'null',13,4,7,2,'null','null',5,1],0)
    ss.printf(root3)
```

## 根据遍历所衍生
## [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)[Liu]
层序遍历是不能用递归来写的，这做这道题之前我们先看广度优先遍历。
```python
import Tree
ss=Tree.TreeNodeTools()
root=ss.createTreeByrow([1,2,3,4,5,6,7],0)
# ss.printf(root3)
class Solution:
    def levelOrder(self, root):
        if not root:return []
        quene=[]
        quene.append(root)
        res=[]
        while len(quene)>0:
            node=quene.pop(0)
            res.append(node.val)
            if node.left:
                quene.append(node.left)
            if node.right:
                quene.append(node.right)
        return res
ss=Solution()
print(ss.levelOrder(root))

```
将队列改成栈就是深度优先：
```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        if not root:return []
        stack=[]
        stack.append(root)
        res=[]
        while len(stack)>0:
            temp=[]
            for i in range(len(stack)):
                node=stack.pop()#改成直接pop
                temp.append(node.val)
                if node.left:
                    stack.append(node.left)
                if node.right:
                    stack.append(node.right)
            res.append(temp[:])
        return res
```
利用list的特性来改造成层序遍历：
```python
import Tree
ss=Tree.TreeNodeTools()
root=ss.createTreeByrow([1,2,3,4,5,6,7],0)
# ss.printf(root3)
class Solution:
    def levelOrder(self, root):
        if not root:return []
        stack=[]
        stack.append(root)
        res=[]
        while len(stack)>0:
            temp=[]
            for i in range(len(stack)):
                node=stack.pop(0)
                temp.append(node.val)
                if node.left:
                    stack.append(node.left)
                if node.right:
                    stack.append(node.right)
            res.append(temp[:])
        return res
ss=Solution()
print(ss.levelOrder(root))
```

## 根据树高所衍生
[543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

[104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)[Liu]