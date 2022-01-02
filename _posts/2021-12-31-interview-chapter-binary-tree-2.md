---
title: 面试之 Binary tree 篇 (2)
tags: [Algorithm]
---

## Q1 翻转二叉树
[leetcode:easy](https://leetcode-cn.com/problems/invert-binary-tree/)

题目：翻转一棵二叉树。

示例：
```
输入：
     4
   /   \
  2     7
 / \   / \
1   3 6   9

输出：
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

笔记：只要把每一个节点的左右孩子翻转一下，就可以达到整体翻转的效果。这道题目使用前序遍历和后序遍历都可以。中序遍历会把某些节点的左右孩子翻转两次。

```
# 深度优先遍历 前序遍历
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return None
        root.left, root.right = root.right, root.left #中
        self.invertTree(root.left) #左
        self.invertTree(root.right) #右
        return root
```

## Q2 对称二叉树
[leetcode:easy](https://leetcode-cn.com/problems/symmetric-tree/)

题目：给定一个二叉树，检查它是否是镜像对称的。

示例：
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

```
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if not root:
            return True
        return self.compare(root.left, root.right)
        
    def compare(self, left, right):
        #首先排除空节点的情况
        if left == None and right != None: return False
        elif left != None and right == None: return False
        elif left == None and right == None: return True
        #排除了空节点，再排除数值不相同的情况
        elif left.val != right.val: return False
        
        #此时就是：左右节点都不为空，且数值相同的情况
        #此时才做递归，做下一层的判断
        outside = self.compare(left.left, right.right) #左子树：左、 右子树：右
        inside = self.compare(left.right, right.left) #左子树：右、 右子树：左
        isSame = outside and inside #左子树：中、 右子树：中 （逻辑处理）
        return isSame
```

## Q3 二叉树的最大深度
[leetcode:easy](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

题目：给定一个二叉树，找出其最大深度。二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。叶子节点是指没有子节点的节点。

笔记：本题可以使用前序遍历，也可以使用后序遍历，使用前序求的是深度，使用后序求的是高度。而根节点的高度就是二叉树的最大深度，所以本题中我们通过后序求的根节点高度来求的二叉树最大深度。

```
# 深度优先遍历 后序遍历
class solution:
    def maxdepth(self, root: treenode) -> int:
        return self.getdepth(root)
        
    def getdepth(self, node):
        if not node:
            return 0
        leftdepth = self.getdepth(node.left) #左
        rightdepth = self.getdepth(node.right) #右
        depth = 1 + max(leftdepth, rightdepth) #中
        return depth
```

```
class solution:
    def maxdepth(self, root: treenode) -> int:
        if not root:
            return 0
        return 1 + max(self.maxdepth(root.left), self.maxdepth(root.right))
```

## Q4 N叉树的最大深度
[leetcode:easy](https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/)

题目：给定一个N叉树，找到其最大深度。最大深度是指从根节点到最远叶子节点的最长路径上的节点总数。

```
class solution:
    def maxdepth(self, root: 'node') -> int:
        if not root:
            return 0
        depth = 0
        for i in range(len(root.children)):
            depth = max(depth, self.maxdepth(root.children[i]))
        return depth + 1
```

## Q5 二叉树的最小深度
[leetcode:easy](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

题目：给定一个二叉树，找出其最小深度。最小深度是从根节点到最近叶子节点的最短路径上的节点数量。叶子节点是指没有子节点的节点。

笔记：后序遍历。

```
# 深度优先遍历 后序遍历
class Solution:
    def minDepth(self, root: TreeNode) -> int:
        if not root:
            return 0
        if not root.left and not root.right:
            return 1

        min_depth = 10**9
        if root.left:
            min_depth = min(self.minDepth(root.left), min_depth) # 获得左子树的最小高度
        if root.right:
            min_depth = min(self.minDepth(root.right), min_depth) # 获得右子树的最小高度
        return min_depth + 1
```

## Q6 完全二叉树的节点个数
[leetcode:medium](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

题目：给你一棵**完全二叉树**的根节点 `root`，求出该树的节点个数。

笔记：完全二叉树只有两种情况。情况一：就是满二叉树；情况二：最后一层叶子节点没有满。可以看出如果整个树不是满二叉树，就递归其左右孩子，直到遇到满二叉树为止。

```
# 深度优先遍历 后序遍历
class Solution:
    def countNodes(self, root: TreeNode) -> int:
        return self.getNodesNum(root)
        
    def getNodesNum(self, cur):
        if not cur:
            return 0
        leftNum = self.getNodesNum(cur.left) #左
        rightNum = self.getNodesNum(cur.right) #右
        treeNum = leftNum + rightNum + 1 #中
        return treeNum
```

```
class Solution:
    def countNodes(self, root: TreeNode) -> int:
        if not root:
            return 0
        return 1 + self.countNodes(root.left) + self.countNodes(root.right)
```

## Q7 左叶子之和
[leetcode:easy](https://leetcode-cn.com/problems/sum-of-left-leaves/)

题目：计算给定二叉树的所有左叶子之和。

示例：
```
    3
   / \
  9  20
    /  \
   15   7

在这个二叉树中，有两个左叶子，分别是 9 和 15，所以返回 24
```

笔记：

<img src="{{ site.baseurl }}/assets/images/leetcode11.png" style="border:1px solid black;">

首先要注意是判断左叶子，不是二叉树左侧节点。判断当前节点是不是左叶子是无法判断的，必须要通过节点的父节点来判断其左孩子是不是左叶子。如果该节点的左节点不为空，该节点的左节点的左节点为空，该节点的左节点的右节点为空，则找到了一个左叶子。递归的遍历顺序为后序遍历。

```
# 深度优先遍历 后序遍历
class Solution:
    def sumOfLeftLeaves(self, root: TreeNode) -> int:
        if not root: 
            return 0
        
        left_left_leaves_sum = self.sumOfLeftLeaves(root.left)  # 左
        right_left_leaves_sum = self.sumOfLeftLeaves(root.right) # 右
        
        cur_left_leaf_val = 0
        if root.left and not root.left.left and not root.left.right: 
            cur_left_leaf_val = root.left.val 
            
        return cur_left_leaf_val + left_left_leaves_sum + right_left_leaves_sum # 中
```

## Q8 找树左下角的值
[leetcode:medium](https://leetcode-cn.com/problems/find-bottom-left-tree-value/)

题目：给定一个二叉树的根节点 `root`，请找出该二叉树的**最底层最左边**节点的值。假设二叉树中至少有一个节点。

笔记：本题使用层序遍历比递归要好理解。只需要记录最后一行第一个节点的数值就可以了。

```
class Solution:
    def findBottomLeftValue(self, root: TreeNode) -> int:
        queue = deque()
        if root: 
            queue.append(root)
        result = 0
        while queue: 
            q_len = len(queue)
            for i in range(q_len): 
                if i == 0: 
                    result = queue[i].val 
                cur = queue.popleft()
                if cur.left: 
                    queue.append(cur.left)
                if cur.right: 
                    queue.append(cur.right)
        return result
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
