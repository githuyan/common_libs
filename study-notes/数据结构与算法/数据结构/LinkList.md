### 链表

> 链表是一块连续的内存空间,无法高效获取长度，无法根据偏移快速访问元素，是链表的两个劣势。
>
> 在处理链表相关问题的时候注意**哑指针的用处**
>
> **哑指针可以用来删除只有一个元素的链表中唯一的元素**

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
    @staticmethod
    def adds(ele:list):
        if not ele:
            return None
        head = ListNode(ele[0])
        dump = head
        for e in ele[1:]:
            dump.next = ListNode(e)
            dump = dump.next
        return head
    @staticmethod
    def flat(head) -> list:
        result = []
        root = head
        while root:
            result.append(root.val)
            root = root.next
        return result
```



1. #### leetcode第237题 题目：删除链表中的节点，难度：简单

   题目描述

   请编写一个函数，使其可以删除某个链表中给定的（非末尾）节点。传入函数的唯一参数为 **要被删除的节点** 。

    题解

   ```python
   class Solution:
       # 由于只给了要删除的节点，所以将这个节点的值换成写一个节点的值，然后跳过下一个节点就行了，实际上是删除了下一个节点，
       def deleteNode(self, node):
           """
           :type node: ListNode
           :rtype: void Do not return anything, modify node in-place instead.
           """
           # 把node的下一个节点的值赋给node,然后删除node的下一个节点，一个替换
           node.val = node.next.val
           node.next = node.next.next
   ```
   
2. #### LeetCode第21题，题目：合并两个有序链表，难度：简单

   **题目描述：**

   将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

   **题解**

   ```python
   class Solution:
       def mergeTwoLists(self, l1: ListNode, l2: ListNode) -> ListNode:
           # 递归法
           # if not l1:
           #     return l2
           # elif not l2:
           #     return l1
           # elif l1.val < l2.val:
           #     l1.next = self.mergeTwoLists(l1.next,l2)
           #     return l1
           # else:
           #     l2.next = self.mergeTwoLists(l1,l2.next)
           #     return l2
   
           # 迭代法，这个跟合并有序数组差不多
           temp = ListNode()
           head = temp
           while True:
               if not l1:
                   temp.next = l2
                   break
               elif not l2:
                   temp.next = l1
                   break
               if l1.val > l2.val:
                   temp.next = l2
                   l2 = l2.next
               elif l1.val <= l2.val:
                   temp.next = l1
                   l1 = l1.next
               temp = temp.next
           return head.next
   ```

3. #### LeetCode第142题，题目：环形链表II，难度：中等

   **题目简介**

   给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

   **题解**

   ```python
   class Solution:
       def detectCycle(self, head: ListNode) -> ListNode:
           if not head or not head.next:
               return None
           fast,slow = head,head
           while True:
               if not (fast and fast.next):
                   return None
               fast = fast.next.next
               slow = slow.next
               if fast == slow:
                   break
           fast = head
   
           while fast != slow:
               fast = fast.next
               slow = slow.next
           return slow
   ```

4. #### LeetCode第19题，题目：删除链表的倒数第N个节点，难度：中等

   **题目简介**

   给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

   你能尝试使用一趟扫描实现吗？

   **题解**

   ```python
   class Solution:
       def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
           # 双指针
           dummy = ListNode(0, head)
           first = head
           # 注意这里哑指针的用处
           second = dummy
           for i in range(n):
               first = first.next
   
           while first:
               first = first.next
               second = second.next
   
           second.next = second.next.next
           return dummy.next
       
           # # 栈
           # stack = []
           # dummy = ListNode(0,head)
           # cur = dummy
           # while cur:
           #     if not cur:
           #         break
           #     stack.append(cur)
           #     cur = cur.next
           # while n:
           #     stack.pop()
           #     n -= 1
           # node = stack[-1]
           # node.next = node.next.next
           # return dummy.next
           
           # 递归实现
   	    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
               temp = ListNode(0)
               temp.next = head
   
               def dfs(head, n):
                   if not (head and head.next):
                       return head, n
   
                   _, n = dfs(head.next, n)
                   n -= 1
                   if not n:
                       head.next = head.next.next
                   return head, n
   
               return dfs(temp, n)[0].next
   ```
   
5. #### LeetCode第206题，题目：反转链表，难度：简单

   ```python
   class Solution:
       def reverseList(self, head: ListNode):
   		# 递归的方法并没有在原位翻转，而是追加式的翻转
           # if(head==None or head.next==None):
           #     return head
   
           # cur = self.reverseList(head.next)
   
           # head.next.next = head
   
           # head.next = None
   
           # return cur
           
           
           # 堆栈，需要遍历两次链表
           # if not head:
           #     return None
   
           # stack = []
           # while True:
           #     if not head:
           #         break
           #     stack.append(head)
           #     head = head.next
   
           # tail  = stack.pop()
           # temp = tail
           # while stack:
           #     node = stack.pop()
           #     tail.next = node
           #     node.next = None
           #     tail = node
   
           # return temp
   
           # 
   
   
           if not head:
               return None
   
           prev = head
           head = head.next
           # 将第一个节点断开连接
           prev.next = None
   
           while head:
               tail = head.next
   
               head.next = prev
   
               prev = head
               head = tail
   
           return prev
   ```
   
6. #### LeetCode第160题，题目：相交链表，难度：简单

   **题目简介**

   给你两个单链表的头节点 `headA` 和 `headB` ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 `null` 。

   **题解**

   ```python
   # 双指针的用法
   class Solution:
       def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
           # 双指针，让A和B各走一遍A+B两条链表，由于他们走的总路程相同，所以，当走完两条链表时，他们应该是同时到达终点，只是
           # 如果他们有相交的地方，他们再走第二遍的时候相遇共同走完一段路程，若没有相交，则走到最后为空截止，
           # A走过的路程 a+c
           # B走过的路程 b+c
           # a+c+b+c == b+c+a+c 主要是顺序
           A,B = headA,headB
           while headA != headB:
   
               # 事实证明，三元表达式比普通判断要快
               if not headA:
                   headA = B 
               else:
                   # 走到空，也算是一步
                   headA = headA.next
               if not headB:
                   headB = A
               else:
                   headB = headB.next
               
           return headB
   ```

   

