# 树
树的题主要是由两部分组成，其一是结构本身所引起。其中又包含两种方式，一种是计算节点。一种是根据遍历方式所引起的题目。其二是从算法衍生而来，主要是搜索，递归树。
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