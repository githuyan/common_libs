### 栈

> 栈是一种具有**后进先出**性质的线性表的数据结构。
>
> 栈（Stack）又名堆栈，它是一种重要的数据结构。
>
> 从数据结构角度看，栈也是线性表，其特殊性在于栈的基本操作是线性表操作的子集，它是操作受限的线性表，因此，可称为限定性的数据结构。限定它仅在表尾进行插入或删除操作。表尾称为栈顶，相应地，表头称为栈底。
>
> 栈的基本操作除了在栈顶进行插入和删除外，还有栈的初始化，判空以及取栈顶元素等

```python
class MinStack:
    """普通栈"""
    def __init__(self):
        """
        initialize your data structure here.
        """
        self._stack = []
        self._min = min(self._stack)

    def push(self, val: int) -> None:
        self._stack.append(val)

    def pop(self) -> None:
        self._stack.pop()

    def top(self) -> int:
        return self._stack[-1]

    def getMin(self) -> int:
        return min(self._stack)
```

**栈的作用**：因为栈只允许从尾进栈出栈，因此保留了**元素的进栈顺序**



1. #### leetcode 第115题，题目：最小栈 ，难度：简单

   > 实现一个栈，getMin()方法是关键

   **题目描述：**

   设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

   push(x) —— 将元素 x 推入栈中。
   pop() —— 删除栈顶的元素。
   top() —— 获取栈顶元素。
   getMin() —— 检索栈中的最小元素。

   **解法**

   ```python
   class MinStack1:
       """辅助栈，不用在求最小值时，在遍历所有元素"""
       def __init__(self):
           self.stack = []
           self.min_stack = []
       def push(self, x: int) -> None:
           self.stack.append(x)
           if not self.min_stack or x <= self.min_stack[-1]: 
               self.min_stack.append(x)
       def pop(self) -> None:
           if self.stack.pop() == self.min_stack[-1]:
               self.min_stack.pop()
       def top(self) -> int:
           return self.stack[-1]
       def getMin(self) -> int:
           return self.min_stack[-1]
   
   class MinStack2(object):
       """
       另一种辅助栈，更好理解，虽然只是用了一个原栈，但是，一个栈元素中存储了两个值，
       和辅助栈的空间占用相同，但在复杂栈情况下会有些乱
       """
       def __init__(self):
           """
           initialize your data structure here.
           """
           self.stack = []
   
       def push(self, x):
           """
           :type x: int
           :rtype: void
           """
           if not self.stack:
               self.stack.append((x, x))
           else:
               self.stack.append((x, min(x, self.stack[-1][1])))
   
       def pop(self):
           """
           :rtype: void
           """
           self.stack.pop()
   
       def top(self):
           """
           :rtype: int
           """
           return self.stack[-1][0]
   
       def getMin(self):
           """
           :rtype: int
           """
           return self.stack[-1][1]
   
   class MinStack3:
       # 不使用额外空间的辅助栈
       def __init__(self):
           """
           initialize your data structure here.
           """
           self.stack = []
           self.min_value = -1
   
       def push(self, x: int) -> None:
           if not self.stack:
               self.stack.append(0)
               self.min_value = x
           else:
               diff = x-self.min_value
               self.stack.append(diff)
               self.min_value = self.min_value if diff > 0 else x
   
       def pop(self) -> None:
           if self.stack:
               diff = self.stack.pop()
               if diff < 0:
                   top = self.min_value
                   self.min_value = top - diff
               else:
                   top = self.min_value + diff
               return top
   
       def top(self) -> int:
           return self.min_value if self.stack[-1] < 0 else self.stack[-1] + self.min_value
   
       def getMin(self) -> int:
           return self.min_value if self.stack else -1
   ```

   

2. #### leetcode 第496题，题目：**下一个最大的元素**，类型：**简单**

   **题目描述：**

   给你两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。

   请你找出 nums1 中每个元素在 nums2 中的下一个比其大的值。

   nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

   ```
   输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
   输出: [-1,3,-1]
   ```

   **解法**

   ```python
   class Solution:
       def nextGreaterElement(self, nums1, nums2):
           stack = []
           # 构造一个结果的映射
           ret = {}
           for i in nums2:
               while stack and stack[-1]<i:
                   ret[stack[-1]] = i
                   del stack[-1]
               stack.append(i)
           ret.update({s:-1 for s in stack})
           return [ret[num] for num in nums1]
   # leetcode submit region end(Prohibit modification and deletion)
   if __name__ ==  "__main__":
       s = Solution()
       nums1 = [2,3,4,]
       nums2 = [2,1,3,5,6,74,4]
       print(s.nextGreaterElement(nums1,nums2))
   ```

   **时间复杂度分析**：只需要分别遍历一遍num2，nums1，O(n)

3. #### leetcode第20题 题目：有效的括号，难度：简单

   **题目描述：**

   给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

   有效字符串需满足：
   
   左括号必须用相同类型的右括号闭合。
   左括号必须以正确的顺序闭合。
   
   ```
   输入：s = "()"
   输出：true
   ```

   **解法**
   
   ```python
   # 本题具有特殊性，有一个正括号必须有一个对应的反括号，才返回True，
   class Solution:
       def isValid(self, s: str) -> bool:
           if len(s)%2:
               return False
           stack = []
           ret = {
               ")": "(",
               "]": "[",
               "}": "{",
           }
           for i in s:
               if i in ret:
                   # 当前栈不为空，且栈顶不等于当前对应值时返回False，
                   if not stack or stack[-1] != ret[i]:
                       return False
                   # 栈顶匹配时，出栈
                   del stack[-1]
               else:
                   stack.append(i)
           return not stack
   ```
   
4. #### leetcode第739题，题目：每日温度，难度：中等

   **题目描述：**

   请根据每日 气温 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 0 来代替。

   例如，给定一个列表 temperatures = [73, 74, 75, 71, 69, 72, 76, 73]，你的输出应该是 [1, 1, 4, 2, 1, 1, 0, 0]。

   提示：气温 列表长度的范围是 [1, 30000]。每个气温的值的均为华氏度，都是在 [30, 100] 范围内的整数

   **解法:**

   ```python
   class Solution:
       def dailyTemperatures(self, temperatures): 
           """栈的应用，本题难点为去重，"""
           stack = []
           rets = []
           for index,tem in enumerate(temperatures):
               while stack and tem > stack[-1][1]:
                   wait = index-stack[-1][0]
                   rets.append((stack[-1][0],stack[-1][1],wait))
                   del stack[-1]
               stack.append((index,tem))
           rets.extend((s[0],s[1],0) for s in stack)
           return [ret[2] for ret in sorted(rets)]
   ```

   **时间复杂度分析：**需要遍历stack，rets，排序，O(n)

5. #### leetcod第394题，题目：字符串解码，难度：中等

   **题目描述：**

   给定一个经过编码的字符串，返回它解码后的字符串。

   编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。

   你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

   此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入

   ```
   s = "3[a]2[bc]"
   输出："aaabcbc"
   ```

   **解法**

   ```python
   class Solution:
       def decodeString(self, s: str) -> str:
           # 一个栈，将结果分为三部分，之前，加密，之后，将（加密倍数，之后）入栈
           stack,multi,res = [],0,""
           for i in s:
               if i == "[":
                   stack.append((multi,res))
                   multi,res = 0,""
               elif i == "]":
                   curr_multi,curr_res = stack.pop()
                   # 当有多个数字如，100 时，计算总和
                   res = curr_res + curr_multi*res
               elif i.isdigit():
                   multi = multi*10 + int(i)
               else:
                   res = res + i
           return res
   ```

   