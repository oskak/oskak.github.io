---
title: 面试之栈与队列（一）
tags: [Algorithm]
---

## 基础
队列是先进先出，栈是先进后出。

栈是一种操作受限的线性表，只允许从一端插入和删除数据。栈的一个最重要的特征就是栈的插入和删除只能在栈顶进行，所以每次删除的元素都是最后进栈的元素，故栈也被称为后进先出（LIFO）表。每个栈都有一个栈顶指针，它的初始值为 `-1`，且总是指向最后一个入栈的元素。栈有两种处理方式，即进栈（push）和出栈（pop）。

队列是一种先入先出（FIFO）的线性表，类似于现实中排队时的队列（队尾进，队头出），队列只在线性表两端进行操作，插入元素的一端称为表尾，删除（取出）元素的一端称为表头。

## Q1 用栈实现队列
[leetcode:easy](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

题目：请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（`push`、`pop`、`peek`、`empty`）。

示例：
```
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```

笔记：在 `push` 数据的时候，把数据放进输入栈。在 `pop` 的时候，输出栈如果为空，就把输入栈数据全部导入进来，再从输出栈弹出数据，如果输出栈不为空，则直接从输出栈弹出数据即可。

如何判断队列为空呢？如果进栈和出栈都为空的话，说明模拟的队列为空了。

```
class MyQueue:

    def __init__(self):
        """
        in主要负责push，out主要负责pop
        """
        self.stack_in = []
        self.stack_out = []

    def push(self, x: int) -> None:
        """
        有新元素进来，就往in里面push
        """
        self.stack_in.append(x)

    def pop(self) -> int:
        """
        Removes the element from in front of queue and returns that element.
        """
        if self.empty():
            return None
        
        if self.stack_out:
            return self.stack_out.pop()
        else:
            for i in range(len(self.stack_in)):
                self.stack_out.append(self.stack_in.pop())
            return self.stack_out.pop()

    def peek(self) -> int:
        """
        Get the front element.
        """
        ans = self.pop()
        self.stack_out.append(ans)
        return ans

    def empty(self) -> bool:
        """
        只要in或者out有元素，说明队列不为空
        """
        return not (self.stack_in or self.stack_out)


# Your MyQueue object will be instantiated and called as such:
# obj = MyQueue()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.peek()
# param_4 = obj.empty()
```

## Q2 用队列实现栈
[leetcode:easy](https://leetcode-cn.com/problems/implement-stack-using-queues/)

题目：请你仅使用两个队列实现一个后入先出（LIFO）的栈，并支持普通栈的全部四种操作（`push`、`top`、`pop` 和 `empty`）。

示例：
```
输入：
["MyStack", "push", "push", "top", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 2, 2, false]

解释：
MyStack myStack = new MyStack();
myStack.push(1);
myStack.push(2);
myStack.top(); // 返回 2
myStack.pop(); // 返回 2
myStack.empty(); // 返回 False
```

笔记：队列是先进先出的规则，把一个队列中的数据导入另一个队列中，数据的顺序并没有变，并没有变成先进后出的顺序。

```
from collections import deque

class MyStack:

    def __init__(self):
        """
        in - 存所有数据
        out - 仅在pop的时候会用到
        """
        self.queue_in = deque()
        self.queue_out = deque()

    def push(self, x: int) -> None:
        """
        直接append即可
        """
        self.queue_in.append(x)

    def pop(self) -> int:
        """
        1. 首先确认不空
        2. 因为队列的特殊性，FIFO，所以我们只有在pop()的时候才会使用queue_out
        3. 先把queue_in中的所有元素（除了最后一个），依次出列放进queue_out
        4. 交换in和out，此时out里只有一个元素
        5. 把out中的pop出来，即是原队列的最后一个
        """
        if self.empty():
            return None

        for i in range(len(self.queue_in) - 1):
            self.queue_out.append(self.queue_in.popleft())
        
        self.queue_in, self.queue_out = self.queue_out, self.queue_in    
        return self.queue_out.popleft()

    def top(self) -> int:
        """
        1. 首先确认不空
        2. 我们仅有in会存放数据，所以返回第一个即可
        """
        if self.empty():
            return None
        
        return self.queue_in[-1]

    def empty(self) -> bool:
        """
        因为只有in存了数据，只要判断in是不是有数即可
        """
        return len(self.queue_in) == 0


# Your MyStack object will be instantiated and called as such:
# obj = MyStack()
# obj.push(x)
# param_2 = obj.pop()
# param_3 = obj.top()
# param_4 = obj.empty()
```

## References
- [leetcode-master](https://github.com/youngyangyang04/leetcode-master)
