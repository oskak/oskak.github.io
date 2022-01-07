---
title: 面试之 Binary tree 篇 (3)
tags: [Algorithm]
---

## Q1 合并二叉树
[leetcode:easy](https://leetcode-cn.com/problems/merge-two-binary-trees/)

题目：给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 `NULL` 的节点将直接作为新二叉树的节点。合并必须从两个树的根节点开始。

示例：
```
输入: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
输出: 
合并后的树:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
```

```
# 前序遍历
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: TreeNode, root2: TreeNode) -> TreeNode:
        # 递归终止条件: 
        #  但凡有一个节点为空, 就立刻返回另外一个. 如果另外一个也为None就直接返回None. 
        if not root1: 
            return root2
        if not root2: 
            return root1
        # 上面的递归终止条件保证了代码执行到这里root1, root2都非空. 
        root1.val += root2.val # 中
        root1.left = self.mergeTrees(root1.left, root2.left) #左
        root1.right = self.mergeTrees(root1.right, root2.right) # 右
        
        return root1 # 本题我们重复使用了题目给出的节点而不是创建新节点. 节省时间, 空间. 
```

## Q2 二叉搜索树中的搜索
[leetcode:easy](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

题目：给定二叉搜索树（BST）的根节点和一个值。你需要在BST中找到节点值等于给定值的节点。返回以该节点为根的子树。如果节点不存在，则返回 `NULL`。

示例：
```
给定二叉搜索树:

        4
       / \
      2   7
     / \
    1   3

和值: 2

你应该返回如下子树:

      2     
     / \   
    1   3
```

笔记：利用二叉搜索树有序的特点。

```
class Solution:
    def searchBST(self, root: TreeNode, val: int) -> TreeNode:
        # 为什么要有返回值: 
        #   因为搜索到目标节点就要立即return，
        #   这样才是找到节点就返回（搜索某一条边），如果不加return，就是遍历整棵树了。

        if not root or root.val == val: 
            return root

        if root.val > val: 
            return self.searchBST(root.left, val)

        if root.val < val: 
            return self.searchBST(root.right, val)
```

## Q3 验证二叉搜索树
[leetcode:medium](https://leetcode-cn.com/problems/validate-binary-search-tree/)

题目：给你一个二叉树的根节点 `root`，判断其是否是一个有效的二叉搜索树。有效二叉搜索树定义如下：
- 节点的左子树只包含**小于**当前节点的数
- 节点的右子树只包含**大于**当前节点的数
- 所有左子树和右子树自身必须也是二叉搜索树

笔记：中序遍历下，输出的二叉搜索树节点的数值是有序序列。有了这个特性，验证二叉搜索树，就相当于变成了判断一个序列是不是递增的。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        res = []
        def helper(root):
            if not root:
                return 
            helper(root.left)
            res.append(root.val)
            helper(root.right)
        helper(root)
        return res == sorted(res) and len(set(res)) == len(res)
```

## Q4 二叉搜索树的最小绝对差
[leetcode:easy](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/)

题目：给你一个二叉搜索树的根节点 `root`，返回树中任意两不同节点值之间的最小差值。差值是一个正数，其数值等于两值之差的绝对值。

```
class Solution:
    def getMinimumDifference(self, root: TreeNode) -> int:
        res = []   
        r = float("inf")
        def buildaList(root):  #把二叉搜索树转换成有序数组
            if not root: return None
            if root.left: buildaList(root.left)  #左
            res.append(root.val)  #中
            if root.right: buildaList(root.right)  #右
            return res
            
        buildaList(root)
        for i in range(len(res)-1):  # 统计有序数组的最小差值
            r = min(abs(res[i]-res[i+1]),r)
        return r
```

## Q5 二叉搜索树的最近公共祖先
[leetcode:easy](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

题目：给定一个二叉搜索树，找到该树中两个指定节点的最近公共祖先。

说明：
- 所有节点的值都是唯一的
- p、q 为不同节点且均存在于给定的二叉搜索树中

笔记：二叉搜索树是有序的。普通二叉树求最近公共祖先需要使用回溯，从底向上查找，二叉搜索树就不用了，因为搜索树有序（相当于自带方向），只要从上向下遍历就可以。`cur` 节点是数值在 `[p, q]` 区间中则说明该节点 `cur` 就是最近公共祖先。

```
class Solution:
    """二叉搜索树的最近公共祖先 递归法"""

    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if root.val > p.val and root.val > q.val:
            return self.lowestCommonAncestor(root.left, p, q)
        if root.val < p.val and root.val < q.val:
            return self.lowestCommonAncestor(root.right, p, q)
        return root
```

## Q6 二叉搜索树中的插入操作
[leetcode:medium](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

题目：给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。返回插入后二叉搜索树的根节点。输入数据保证，新值和原始二叉搜索树中的任意节点值都不同。注意，可能存在多种有效的插入方式，只要树在插入后仍保持为二叉搜索树即可。你可以返回任意有效的结果。

笔记：不用重构搜索树。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def insertIntoBST(self, root: TreeNode, val: int) -> TreeNode:
        # 返回更新后的以当前root为根节点的新树，方便用于更新上一层的父子节点关系链

        # Base Case
        if not root: return TreeNode(val)

        # 单层递归逻辑:
        if val < root.val: 
            # 将val插入至当前root的左子树中合适的位置
            # 并更新当前root的左子树为包含目标val的新左子树
            root.left = self.insertIntoBST(root.left, val)

        if root.val < val:
            # 将val插入至当前root的右子树中合适的位置
            # 并更新当前root的右子树为包含目标val的新右子树
            root.right = self.insertIntoBST(root.right, val)

        # 返回更新后的以当前root为根节点的新树
        return root
```

## Q7 修剪二叉搜索树
[leetcode:medium](https://leetcode-cn.com/problems/trim-a-binary-search-tree/)

笔记：给你二叉搜索树的根节点 `root`，同时给定最小边界 `low` 和最大边界 `high`。通过修剪二叉搜索树，使得所有节点的值在 `[low, high]` 中。修剪树不应该改变保留在树中的元素的相对结构（即，如果没有被移除，原有的父代子代关系都应当保留）。可以证明，存在唯一的答案。所以结果应当返回修剪好的二叉搜索树的新的根节点。注意，根节点可能会根据给定的边界发生改变。

```
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def trimBST(self, root: TreeNode, low: int, high: int) -> TreeNode:
        '''
        确认递归函数参数以及返回值：返回更新后剪枝后的当前root节点
        '''
        # Base Case
        if not root: return None

        # 单层递归逻辑
        if root.val < low:
            # 若当前root节点小于左界：只考虑其右子树，用于替代更新后的其本身，抛弃其左子树整体
            return self.trimBST(root.right, low, high)
        
        if high < root.val:
            # 若当前root节点大于右界：只考虑其左子树，用于替代更新后的其本身，抛弃其右子树整体
            return self.trimBST(root.left, low, high)

        if low <= root.val <= high:
            root.left = self.trimBST(root.left, low, high)
            root.right = self.trimBST(root.right, low, high)
            # 返回更新后的剪枝过的当前节点root
            return root
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
