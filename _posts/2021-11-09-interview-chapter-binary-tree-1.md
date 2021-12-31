---
title: 面试之 Binary tree 篇 (1)
tags: [Algorithm]
---

## 二叉树的种类
### 满二叉树
如果一棵二叉树只有度为0的结点和度为2的结点，并且度为0的结点在同一层上，则这棵二叉树为满二叉树。

### 完全二叉树
在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。

### 二叉搜索树
二叉搜索树是一个有序树。若它的左子树不空，则左子树上所有结点的值均小于它的根结点的值；若它的右子树不空，则右子树上所有结点的值均大于它的根结点的值；它的左、右子树也分别为二叉搜索树。

### 平衡二叉搜索树
又被称为AVL（Adelson-Velsky and Landis）树，且具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉搜索树。

## 二叉树的遍历方式
- 深度优先遍历
  - 前序遍历
  - 中序遍历
  - 后序遍历
- 广度优先遍历
  - 层次遍历

前中后，其实指的就是中间节点的遍历顺序：
- 前序遍历：中左右
- 中序遍历：左中右
- 后序遍历：左右中

## 二叉树的定义
```
class TreeNode: 
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```

## 二叉树的深度优先遍历
```
# 深度优先遍历 前序遍历
class Solution:
    def preorderTraversal(self, root: TreeNode) -> List[int]:
        # 保存结果
        result = []
        
        def traversal(root: TreeNode):
            if root == None:
                return
            result.append(root.val) # 前序
            traversal(root.left)    # 左
            traversal(root.right)   # 右

        traversal(root)
        return result

# 深度优先遍历 中序遍历
class Solution:
    def inorderTraversal(self, root: TreeNode) -> List[int]:
        result = []

        def traversal(root: TreeNode):
            if root == None:
                return
            traversal(root.left)    # 左
            result.append(root.val) # 中序
            traversal(root.right)   # 右

        traversal(root)
        return result

# 深度优先遍历 后序遍历
class Solution:
    def postorderTraversal(self, root: TreeNode) -> List[int]:
        result = []

        def traversal(root: TreeNode):
            if root == None:
                return
            traversal(root.left)    # 左
            traversal(root.right)   # 右
            result.append(root.val) # 后序

        traversal(root)
        return result
```

## Q1 二叉树的广度优先遍历 (层序遍历)
[leetcode:medium](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

题目：给你一个二叉树，请你返回其按**层序遍历**得到的节点值（即逐层地，从左到右访问所有节点）。

笔记：层序遍历方式就是图论中的广度优先遍历，只不过我们应用在二叉树上。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        res = []
        def helper(root, depth):
            if not root: 
                return []
            if len(res) == depth: 
                res.append([]) # start the current depth
            res[depth].append(root.val) # fulfill the current depth
            if  root.left: 
                helper(root.left, depth + 1) # process child nodes for the next depth
            if  root.right: 
                helper(root.right, depth + 1)
        helper(root, 0)
        return res
```

```
class Solution:
    """二叉树的广度优先遍历 层序遍历 迭代解法"""

    def levelOrder(self, root: TreeNode) -> List[List[int]]:
        results = []
        if not root:
            return results
        
        from collections import deque
        que = deque([root])
        
        while que:
            size = len(que)
            result = []
            for _ in range(size):
                cur = que.popleft()
                result.append(cur.val)
                if cur.left:
                    que.append(cur.left)
                if cur.right:
                    que.append(cur.right)
            results.append(result)

        return results
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
