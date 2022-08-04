### 方法

> **在考虑递归和动态规划问题的时候可以先画出它的树形图**

- **找到特殊性**

  像是有序的二维矩阵

  - LeetCode第240. 题，题目：搜索二维矩阵 II，难度：中等  **List**

- **二分查找**

  用于已经排好序（或者分段每一段是有序的 ）的数组

  - LeetCode第33题，题目：搜索旋转排序数组，难度：中等 **List**
  - LeetCode第34题，题目： 在排序数组中查找元素的第一个和最后一个位置，难度：中等 **List**
  - LeetCode第162. 题，题目：寻找峰值，难度：中等 **List**

- **位运算**

  位运算，在正整数时，左移相当于乘二，右移相当于除二

- **贪心**

  > 只收集每天的正利润，

  1. 股票问题
     - LeetCode第122题，题目：买卖股票的最佳时机ll ，难度：简单
     - LeetCode第55题，题目：跳跃游戏，难度：中等

- **快慢指针**
  
  1. 可以用来判断中间点问题，只需要控制快指针相对于慢指针的倍数
     - 
  2. 可以用来判断有环无环问题，环问题的解决要画图，把每一段的距离用公式表示出来，就清晰了
     - LeetCode第142题，题目：环形链表II
  
- **先后指针**
  
  1. 可以用来判断倒数第N个问题，就像是滑框，当先指针到达N时，后指针再走，然后同时走，这时，先指针到达终点，则后指针到达倒数第N个点
     - LeetCode第19题，题目：删除链表的倒数第N个节点
  
- **双指针**

  > 链表问题一定要画图，并且双指针走的顺序也很重要

  1. 用来判断路径相交问题，两个指针同时开始走，都走完AB两条路，那么两个指针所走的路径总和应该是相等的，即他们走完时，应该是同时到达重点的，若中间相交，则会在第二圈的时候相遇，共同走完最后一段路程
     - LeetCode第160题，题目：相交链表，难度：简单

- **循环不变量**

  > **循环不变量的用法是，正常的时候动，异常的时候不动（异常的时候不动是为了略过这个元素）**

  1. **本质是双指针**，设置一个循环不变量，在他之前的数组都是已排好序的，他之后的是待排序的
     - LeetCode第283题，题目：移动零，**List**
     - LeetCode第75题，题目：颜色分类，难度：简单 **List**
     - LeetCode第26题，题目：删除有序数组中的重复项 **List**

- **哑节点**
  
  1. 由于单向链表是无法获取前一个节点的，所以从后往前遍历的话，无法判断前一个节点是否存在，设置哑结点，相当于设置了一个边界保护，省去了复杂的边界条件判断
     - LeetCode第19题，题目：删除链表的倒数第N个节点
     - LRU（last recently use)（缓存机制）[146. LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)
  
- **递归和回溯**
  
  1. 可以用来解决走路问题，二叉树相当于有两个方向，也可以四个方向上下左右走，就是回溯，记录当前状态
     - LeetCode第112题，题目：路径总和
     - LeetCode第39题，题目：组合总和 **List**
  
- **动态规划**
  
  1. 就是递归和回溯的优化
     - leetcode 第198题，题目：打家劫舍
  
- **抑或操作** ^ 

  > 抑或运算可以用来去重，两个相同的元素抑或是零，三个相同的元素抑或是自身
  
  1. 一个数和 0 做 XOR 运算等于本身：a⊕0 = a
  
     一个数和其本身做 XOR 运算等于 0：a⊕a = 0
  
     XOR 运算满足**交换律和结合律**：a⊕b⊕a = (a⊕a)⊕b = 0⊕b = b
  
     - LeetCode第136题，题目：只出现一次的数字 **List**
  
- **标记法**

  1. 标记法，主要用于不使用额外空间，找到某个元素，标记这个元素的关键在于后续可以通过简单的方法回复对这个元素的访问，使用
     - LeetCode第448题，题目：找到所有数组中消失的数字 **List**

1. #### 快慢指针

   > 定义两个变量，一般是用来确认可迭代对象的中点

   ```python
   class Solution:
       def isPalindrome(self, head: ListNode) -> bool:
           # 首先第一个元素压入栈
           stack = [head.val]
           fast, slow = head, head
           fast_val,slow_val = fast.val,slow.val
           while fast.next and fast.next.next:
               # 将下一个元素压入栈，再开始行进指针
               # 如果可迭代对象是偶数，则慢指针停在中间靠左，快指针停在nums[-2]，此时已将慢指针最后指向的元素压入栈
               # 如果可迭代对象是奇数，则慢指针停在中间，快指针停在结尾
               stack.append(slow.next.val)
               slow = slow.next
               fast = fast.next.next
           # 如果快指针的下一个节点存在，说明是偶数个元素，反之为奇数个元素
           print(slow.val,stack[-1])
           if not fast.next:
               stack.pop()
               slow = slow.next
           else:
               slow = slow.next
           print(slow.val)
           while stack:
               if stack.pop() != slow.val:
                   return False
               slow = slow.next
           return True
   ```

   **用途**

   1. 判断链表的有环无环问题

      ```python
      class Solution1:
          def hasCycle(self, head) -> bool:
              # 这里可以看做在链表的head之前还有一个空节点，slow走一步到达head节点，fast走两步到达head.next节点，这样做是为了，下方while循环的条件
              if not head or not head.next:
                  return False
      
              slow = head
              fast = head.next
      
              while slow != fast:
                  if not fast or not fast.next:
                      return False
                  slow = slow.next
                  fast = fast.next.next
      
              return True
      ```

   2. LeetCode第142题，题目：环形链表II，难度：中等

      **题目简介**

      给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

      **题解**

      ```python
      class Solution:
          def detectCycle(self, head: ListNode) -> ListNode:
              # 画图就很好理解了，标出x,y,z
              # 思路，设从head到环入口的节点数为x,fast和slow第一次相遇时，从环入口到slow的位置为y,则slow走过的路径为x+y,设从第一次相遇点再到环入口的距离为z,则fast走过的路径为x+y+z+y，fast指针走过的路径为slow走过路径的两倍，可得2(x+y) = x+y+z+y,即，x=z,也就是说，slow从现在的位置再走x就会到达环入口，而从head到环入口的距离也为x,让fast指针从头开始，一次走一步，当fast再次和slow相遇时，即为环入口
              if not head or not head.next:
                  return None
              fast,slow = head,head
              # 首先确认链表有没有环
              while True:
                  if not (fast and fast.next):
                      return None
                  fast = fast.next.next
                  slow = slow.next
                  if fast == slow:
                      break
              fast = head
      		# 此时slow再走x就会到达环入口
              while fast != slow:
                  fast = fast.next
                  slow = slow.next
              return slow
      ```

2. #### 前后指针

   > 先后指针常用来在只扫描一遍的情况下，确定倒数第N个元素

   **先指针先走，走N步后，后指针再走，两个指针之间相隔N步，则先指针到达中点时，后指针就在链表的倒数第N位**

   1. LeetCode第19题，题目：删除链表的倒数第N个节点，难度：中等

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
              # 由于我们无法在后的某个节点后，再后的它的前节点，所以直接使用哑结点，填充一个空位，省去了复杂的判断
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
      
      ```

3. #### 双指针

   1. LeetCode第160题，题目：相交链表，难度：简单

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

   

4. #### 递归

   > 递归是用来选方向走路的，如果是一个列表的某一个元素开始递归，那么他可以向两边扩散，如果是一个二维列表，那么可以从某一个元素开始，向上下左右扩散，如果是一棵树，那么可以向左右扩散

   [两种方法](https://blog.csdn.net/QilanAllen/article/details/100806257)，从子问题解决原问题, 无非是两种方法，自底向上(Bottom-Up)与自顶向下(Top-Down)，形式上前者对应iteration，利用循环将结果存在数组里，从数组起始位置向后计算；后者对应recursion，即利用函数调用自身实现。如果不存储上一个状态的解，则为递归，否则就是DP。

   - 自底向上 指的是先找到已知的解，从第一个开始，一直累加计算结果，直到最后求出最终值
   - 自顶向下 指的是，直接到未知解，然后一步步的推算前一步是不是成立，直到到达最开始的已知解处，若满足已知解，则求出结果

   **斐波那契数列问题**

   ![斐波那契数列图片](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20210708205533989.png)

   ```python
   # 这是自顶向下的求法，
   # 直接定义最终值就等于 ret == left+right,然后求left 和 right
   def Fibonacci(n):
       if n < 2:
           return n - 1
       # 这里网上一部分写的有问题，斐波那契数列是从0开始的，
       # 可以看到5=3+2，3=2+1,2=1+1,1=1=0，
       if n == 2 or n == 3:
           return n
   
       left = Fibonacci(n - 1)
       right = Fibonacci(n - 2)
   
       return left + right
   
   # 这个是自底向上的求法
   # 从已知开始推，知道推导要找的值
   def Fibonacci(n):
       ret = [0,1]
       for i in range(2,n):
           one,two = ret[i-2],ret[i-1]
           ret.append(one + two)
       return ret[-1]
   ```

   

   ```python
   # 自底向上的递归
   # 先走到无路可走，假如是一棵树，那么最后判断的必然是某一条路线的最后一个节点
     	1
      / \
     2   3
    / \   \
   4  5    6
   
   def dfs(root):
       if not root:
           return
       
       left = dfs(rooot.left)
       right = dfs(root.right)
       
       # 最后一定是判断最后一个节点的左右节点，递归只关心跟这个节点相连的左右，另一个左右是本地递归结束返回left,然后涉及到的就是上一层了
       if left and right:
           pass
       return 
   ```

   

   ```python
   def dfs(root):
       """deepth是一个递归函数，当调用这个函数，"""
       if not root:
           return 0
       return dfs(root.left) + 1
   """
   当在这里调用这个函数，dfs(root)
   dfs(root.left)+1
   dfs(root.left.left)+1+1
   def(root.left.left.left)+1+1+1
   ...
   直到其中一个dfs返回0，然后就是累加
   本函数的结束条件是，节点为空时，即左边最深的叶子，当他返回上一层时，递增1
   """
   ```

   ##### 回溯法

   > 回溯法就是在递归的时候保留上一步的值，在分支失败返回时，根部有之前缓存的值

   **回溯VS递归**

   很多人认为回溯和递归是一样的，其实不然。在回溯法中可以看到有递归的身影，但是两者是有区别的。

   回溯法从问题本身出发，寻找可能实现的所有情况。和穷举法的思想相近，不同在于穷举法是将所有的情况都列举出来以后再一一筛选，而回溯法在列举过程如果发现当前情况根本不可能存在，就停止后续的所有工作，返回上一步进行新的尝试。

   递归是从问题的结果出发，例如求 n！，要想知道 n！的结果，就需要知道 n*(n-1)! 的结果，而要想知道 (n-1)! 结果，就需要提前知道 (n-1)*(n-2)!。这样不断地向自己提问，不断地调用自己的思想就是递归。

   回溯和递归唯一的联系就是，回溯法可以用递归思想实现。

   ```python
   # LeetCode第112题，题目：路径总和，难度：简单
   
   class Solution:
       def __init__(self):
           self.ret = False
   
       def hasPathSum(self, root: TreeNode, targetSum: int) -> bool:
           # 回溯法，
           # 回溯法就是在递归的时候保留上一步的值，在分支失败返回时，根部有之前缓存的值，
           def dfs(root, targetSum):
               if not root:
                   return
   
               if not root.left and not root.right and root.val == targetSum:
                   self.ret = True
   
               if root.left:
                   root.left.val += root.val
               dfs(root.left, targetSum)
   
               if root.right:
                   root.right.val += root.val
               dfs(root.right, targetSum)
   
           dfs(root, targetSum)
           return self.ret
   ```

   **深信服笔试题，题目：木材长度拼接，难度：简单**

   **题目描述：**

   给一个长度length，给一个数组woods= [1,2,3,4,5]，数组中树每一根木材的长度，不重复，每根木材不限量供应，求拼接成length，所需的最少木材树，
   
   **题解**
   
   ```python
   # 这是一个贪心
   
   def get_count(woods,length,ret):
       if length == 0:
           return True
   
       for i in range(len(woods)):
           if length-woods[i]==0:
               return ret+1
           elif length-woods[i] > 0:
               ret = get_count(woods[i:],length-woods[i],ret+1)
               return ret # 这个递归的返回条件，就像是一个只有左枝的二叉树一样，所有的任务到此结束，
   
   # woods = eval(input().strip())
   # length = int(input().strip())
   woods = [1, 2, 3, 5 ]
   woods.sort(reverse=True)
   length = 9
   print(get_count(woods,length,0))
   ```
   
   
   
   1. ##### LeetCode第39题，题目：组合总和，难度：中等
   
      > 几乎是遍历搜索
   
      ```python
      class Solution:
          def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
              candidates.sort()
              ret = []
              def dfs(ret,target,temp):
                  if target == 0:
                      t = sorted(temp)
                      if t not in ret:
                          ret.append(t )
                          return
      
                  for candidate in candidates:
                      if candidate <= target:
                          temp.append(candidate)
                          dfs(ret,target-candidate,temp)
                          temp.pop()
                      else:
                          break
              temp = []
              dfs(ret,target,temp)
              return ret
      ```
   
      
   
   **广度优先搜索**
   
   > 使用队列，使用计数的方式确定本层是否遍历结束
   
   #### 动态规划
   
   >  动态规划，就保存上一步的最优解
   
   1. ##### LeetCode第53题，题目：最大子序和，难度：简单
   
      题目描述
   
      题解
   
      ```python
      class Solution3:
          def maxSubArray(self, nums: List[int]) -> int:
              # 动态规划，由于不确定哪个元素被取，所以就计算每个元素都取时的最大值
              for i in range(1,len(nums)):
                  # 确定以这个元素结尾的最大值
                  # 将上一步的最大值加上当前值 再与 当前值进行比较，这是动态规划的核心，保存上一步的最优解，这就保证了连续的问题
                  # 这样的话，每一个元素都有两个选择，即取或者不取，变成了，只有两个选择，取不取当前元素，因为上一步已经是最优解了，必须要取，
                  # 最优解的最小值也要 >= 0 
                  nums[i] = max(nums[i-1]+nums[i],nums[i])
              return max(nums)
      
      
      class Solution:
          def maxSubArray(self, nums: List[int]) -> int:
              if len(nums) <2:
                  return nums[0]
      
              nets = [[] for i in range(len(nums))]
              nets[0].extend([0,nums[0]])
      
              for i in range(1,len(nums)):
                  not_do = max(nets[i-1])
                  do = max([nums[i]+nets[i-1][1],nums[i]])
                  nets[i].extend([not_do,do])
              ret = max(nums) if max(nets[-1]) == 0 else max(nets[-1])
      
              return ret
          
      # 这种解法是，只求增益
      class Solution1:
          def maxSubArray(self, nums) -> int:
              res = [max(nums)]
              n = 0
              # 如果连取两个值的和大于零，说明是正增益，就可以继续取，肯定比继续取得下一个值大
              for i in range(len(nums)):
                  if nums[i] + n > 0:
                      n = n + nums[i]
                      res.append(n)
                  else:
                      n = 0
              return max(res)
      ```
   
   
   ##### 贪心
   
   1. **LeetCode第122题，题目：买卖股票的最佳时机ll ，难度：简单**
   
      **题目描述**
   
      给定一个数组 prices ，其中 prices[i] 是一支给定股票第 i 天的价格。
   
      设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
   
      注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
   
      **题解**
   
      ```python
      class Solution:
          # 只收集每天的正利润
          def maxProfit(self, prices) -> int:
              minprice = float('inf')
              ret = 0
              for i in range(len(prices)):
                  value =  prices[i]-minprice
                  if value > 0:
                      ret += value
                  minprice = prices[i]
      
              return ret
      ```
      
   2. **LeetCode第55题，题目：跳跃游戏，难度：中等**
   
      **题目描述**
   
      给定一个非负整数数组 nums ，你最初位于数组的 第一个下标 。 
   
       数组中的每个元素代表你在该位置可以跳跃的最大长度。 
   
       判断你是否能够到达最后一个下标。 
   
      **题解**
   
      ```python
      class Solution:
          def canJump(self, nums: List[int]) -> bool:
              p = 0
              for i,step in enumerate(nums):
                  # 只更新最远距离，别的一概不管
                  if p >= i and step+i > p:
                      p = step+i
      
              return p >= len(nums)-1
      ```
