---
title: 面试之链表篇（二）
tags: [Algorithm]
---

## Q1 反转链表
[leetcode:easy](https://leetcode-cn.com/problems/reverse-linked-list/)

题目：给你单链表的头节点 `head`，请你反转链表，并返回反转后的链表。

笔记：
首先定义一个 `cur` 指针，指向头结点，再定义一个 `pre` 指针，初始化为 `null`。然后开始反转，首先要把 `cur->next` 节点用 `tmp` 指针保存一下。为什么要保存一下这个节点呢，因为接下来要改变 `cur->next` 的指向了，将 `cur->next` 指向 `pre`，此时已经反转了第一个节点。接下来，就是循环走如下代码逻辑了，继续移动 `pre` 和 `cur` 指针。最后，`cur` 指针已经指向了 `null`，循环结束，链表也反转完毕了。此时我们 `return pre` 指针就可以了，`pre` 指针就指向了新的头结点。

递归法相对抽象一些，但是其实和双指针法是一样的逻辑，同样是当 `cur` 为空的时候循环结束，不断将 `cur` 指向 `pre` 的过程。

迭代法：
```
#双指针
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: ListNode) -> ListNode: 
        pre = None
        cur = head  
        while cur != None:
            temp = cur.next #保存cur的下一个节点
            cur.next = pre #反转
            #更新pre、cur指针
            pre = cur
            cur = temp
        return pre
```

递归法：
```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        
        def reverse(pre, cur):
            if not cur:
                return pre
                
            tmp = cur.next
            cur.next = pre

            return reverse(cur, tmp)
        
        return reverse(None, head)
```

## Q2 两两交换链表中的节点
[leetcode:medium](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

题目：给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

笔记：
<img src="{{ site.baseurl }}/assets/images/leetcode2.png" style="border:1px solid black;">

时间复杂度：O(n)
空间复杂度：O(1)

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next

class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        res = ListNode(next=head)
        pre = res
        
        # 必须有pre的下一个和下下个才能交换，否则说明已经交换结束
        while pre.next and pre.next.next:
            cur = pre.next
            post = pre.next.next
            
            # pre，cur，post对应最左，中间的，最右边的节点
            cur.next = post.next
            post.next = cur
            pre.next = post

            pre = pre.next.next
        return res.next
```

## Q3 删除链表的倒数第 N 个结点
[leetcode:medium](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

题目：给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

进阶：你能尝试使用一趟扫描实现吗？

笔记：双指针的经典应用，如果要删除倒数第 `n` 个节点，让 `fast` 移动 `n` 步，然后让 `fast` 和 `slow` 同时移动，直到 `fast` 指向链表末尾。删掉 `slow` 所指向的节点就可以了。

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        head_dummy = ListNode()
        head_dummy.next = head

        slow, fast = head_dummy, head_dummy
        while n != 0: #fast先往前走n步
            fast = fast.next
            n -= 1
        while fast.next != None:
            slow = slow.next
            fast = fast.next
        #fast 走到结尾后，slow的下一个节点为倒数第N个节点
        slow.next = slow.next.next #删除
        return head_dummy.next
```

##  Q4 链表相交
[leetcode:easy](https://leetcode-cn.com/problems/intersection-of-two-linked-lists-lcci/)

题目：给你两个单链表的头节点 `headA` 和 `headB`，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 `null`。

<img src="{{ site.baseurl }}/assets/images/leetcode3.png" style="border:1px solid black;">

```
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        """
        根据快慢法则，走的快的一定会追上走得慢的。
        在这道题里，有的链表短，他走完了就去走另一条链表，我们可以理解为走的快的指针。

        那么，只要其中一个链表走完了，就去走另一条链表的路。如果有交点，他们最终一定会在同一个
        位置相遇
        """
        cur_a, cur_b = headA, headB     # 用两个指针代替a和b

        
        while cur_a != cur_b:
            cur_a = cur_a.next if cur_a else headB      # 如果a走完了，那么就切换到b走
            cur_b = cur_b.next if cur_b else headA      # 同理，b走完了就切换到a
        
        return cur_a
```

## Q5 环形链表 II
[leetcode:medium](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

题目：给定一个链表，返回链表开始入环的第一个节点。如果链表无环，则返回 `null`。

说明：不允许修改给定的链表。

进阶：你是否可以使用 `O(1)` 空间解决此题？

笔记：
<img src="{{ site.baseurl }}/assets/images/leetcode1.png" style="border:1px solid black;">

```
class Solution:
    def detectCycle(self, head: ListNode) -> ListNode:
        slow, fast = head, head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            # 如果相遇
            if slow == fast:
                p = head
                q = slow
                while p != q:
                    p = p.next
                    q = q.next
                #你也可以return q
                return p

        return None
```

## References
- [LeetCode 刷题攻略](https://github.com/youngyangyang04/leetcode-master)
