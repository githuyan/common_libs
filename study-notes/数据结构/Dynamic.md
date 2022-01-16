### 动态规划问题

> 动态规划问题依靠需要累加解决小问题，
>
> 动态规划就是对递归的优化，当递归时不记录走过的信息就是递归，记录就是动态规划（DP）

1. #### leetcode 第198题，题目：打家劫舍，难度：中等

   **题目描述**

   你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

   给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

   **题解**一

   > 直接使用状态转移方程
   >
   > 这也太简洁了，让人赞不绝口，这是这个状态转移方程有时候不太好找

   ```python
   class Solution:
       def rob(self, nums: List[int]) -> int:
               pre = cur = 0
               for i in nums:
                   pre, cur = cur, max(pre+i, cur)
               return cur
   ```

   **题解二**

   > 模拟背包问题，企图找出一个通用的方法。构建网格，关键在于抽象出网格的行和列，本题套用背包问题模板，其他问题适不适用，正在验证

   ```python
   class Solution:
       def rob(self, nums: List[int]) -> int:
           if not nums:
               return max(nums)
           # 创建一个房屋数量为列，每个房屋的选择数量为行的网格
           nots = [[] for i in range(len(nums))]
           # 创建一个列表行，含义是 列表中的每个元素房屋偷或不偷的选择
           options = [True, False]
           # 填充第一行初始元素，即只有第一个房屋时，选择偷，那么最大价值就是这个房屋的最大价值，选择不偷，那么最大价值为零
           nots[0].append(nums[0])
           nots[0].append(0)
           # 开始房屋循环
           for i in range(1, len(nums)):
               # 开始选择循环
               for option in range(len(options)):
                   if option<len(options)-1:
                       # 偷这个房屋的价值 == 这个房屋的价值 + 不偷上一个房屋的最大价值
                       do_value = nums[i] + nots[i-1][option + 1]
                       # 填写偷这个房屋的格子
                       nots[i].append(do_value)
   
                       # 不偷这个房屋的价值 == max(偷上一个房屋的最大价值，不偷上一个房屋的最大价值)
                       not_do_value = max(nots[i - 1][option], nots[i - 1][option + 1])
                       # 填写不偷这个房屋的格子
                       nots[i].append(not_do_value)
           return max(nots[-1])
   ```

   复杂度分析：由于使用了网格，**导致内存占用很大**
   
