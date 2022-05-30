### 列表

> 列表是线性表

#### 几种排序方法

**常见排序算法的时空复杂度**

| 排序算法 | 最差时间复杂度 | 平均时间复杂度 | 稳定度 | 空间复杂度    |
| -------- | -------------- | -------------- | ------ | ------------- |
| 冒泡排序 | O(n^2)         | O(n^2)         | 稳定   | O(1)          |
| 选择排序 | O(n^2)         | O(n^2)         | 不稳定 | O(1)          |
| 插入排序 | O(n^2)         | O(n^2)         | 稳定   | O(1)          |
| 快速排序 | O(n^2)         | O(n*log2n)     | 不稳定 | O(log2n)~O(n) |
| 堆排序   | O(n*log2n)     | O(n*log2n)     | 不稳定 | O(1)          |

```python
class Sorted():
    """
    稳定性：相同大小的元素在排序之后依然保持相对位置不变，就是稳定的
    """
    def __init__(self):
        pass
    def quicksort(self,nums):
        """
        # 快速排序
        # 最差时间：O(n^2)	平均空间：O(n*log2n)
        # 不稳定
        # 空间：O(log2n)~O(n)
        快速排序是递归排序，选取一个基准值，把大于这个基准值得放在右边，小于的放在左边，
        在对，left 和 right 进行快速排序，直到某一方数值为一，不可在分为止
        1. Partition：选择基准分割数组为两个子数组，小于基准和大于基准的
        2. 对两个子数组分别快排
        3. 合并结果
        """
        if len(nums) < 2:
            return nums
        left = []
        middle = nums[0]
        right = []
        for i in nums[1:]:
            if i >= nums[0]:
                right.append(i)
            else:
                left.append(i)
        return self.quicksort(left)+[middle]+self.quicksort(right)

    def bubblesort(self,nums):
        """
        # 冒泡排序
        # 最差时间：O(n^2)	平均空间：O(n^2)
        # 稳定
        # 空间：O(1)
        # 快速排序是递归排序，
        冒泡排序是迭代排序，每一个值都与他后面的值比较大小，进行交换位置，所以是O(n2)
        """
        size = len(nums)

        for i in range(1,size):
            for j in range(i,size):
                if nums[j] < nums[i]:
                    nums[j],nums[i] = nums[i],nums[j]

        return nums

    def insertsort(self,nums):
        """
        插入排序
        最差时间 O(n^2)	平均时间：O(n^2)
        稳定
        空间：O(1)
        插入排序，默认某一个元素之前的数组已经是有序的，反向冒泡，交换位置
        """
        size = len(nums)

        for i in range(1,size):
            for j in range(i,-1,-1):
                if nums[j] < nums[j-1]:
                    nums[j],nums[j-1] = nums[j-1],nums[j]
        return nums

    def heapsort(self,nums):
        """
        堆排序
        最大时间：O(n*log2n)	平均时间：O(n*log2n)
        不稳定
        空间：O(1)
        """
        import heapq
        heapq.heapify(nums)
        return [heapq.heappop(nums) for _ in range(len(nums))]

    def mergesort(self,nums):
        """
        归并排序
        空间复杂度很高
        将数组二分拆分，直到为只有单个元素的列表，然后这个列表可以视为有序的，将两个这样的有序列表合并排序
        """
        def merge(left, right):
            middle = []
            l, r = 0, 0
            while True:
                # 判断是否有一个已经为空
                if l >= len(left):
                    middle.extend(right[r:])
                    break
                if r >= len(right):
                    middle.extend(left[l:])
                    break
                if left[l] <= right[r]:
                    middle.append(left[l])
                    l += 1
                elif left[l] >= right[r]:
                    middle.append(right[r])
                    r += 1
            return middle

        def dfs(nums):
            if len(nums) < 2:
                return nums
            l = nums[:len(nums)//2]
            r = nums[len(nums)//2:]

            left = dfs(l)
            right = dfs(r)

            return merge(left,right)
        return dfs(nums)

s = Sorted().mergesort(nums=[2,1,4])

print(s)
```

#### 堆排序

> **完全二叉树的性质：** 在有数组构成的完全二叉树中，知道一个节点的位置，那么就可以判断它的子节点位置
>     left = index * 2 +1
>     right = index *2 + 2

```pythoN
def buildheap(rootindex, nums, limit):
    """
    本函数的作用并非是构造一个大顶堆，而是在大顶堆中维护一个指定节点

    由于整个堆是由数组构成的，没有真实的树结构，所以根据性质，按照层序遍历的方式虚拟出完全二叉树
    性质： 在有数组构成的完全二叉树中，知道一个节点的位置，那么就可以判断它的子节点位置
        left = index * 2 +1
        right = index *2 + 2
    :param rootindex: 根节点位置
    :param nums: 整个数组
    :param limit: 这个很重要，当左右节点位置的值大于limit时，即使在数组中左右节点多对应的元素存在，也不会参与到堆的构建，
    依靠这个进行堆排序过程中，末尾元素的剔除
    :return:
    """
    largest = rootindex  # 这是一个标记，初始假设这个根元素位置为最大元素位置
    left_node_index = rootindex * 2 + 1  # 根据完全二叉树性质，得出左右节点位置
    right_node_index = rootindex * 2 + 2

    # 更新largest代表根，左节点，右节点中最大的位置,
    if left_node_index < limit and nums[left_node_index] > nums[largest]:
        largest = left_node_index
    if right_node_index < limit and nums[right_node_index] > nums[largest]:
        largest = right_node_index

    # 上面到这里是主要的操作方法，主要目的是选出一个根节点和它的两个子节点的最大位置，后面调换之后，
    # 再传入一个改动的节点位置，再比较这个节点和他的左右子节点的值

    # 如果左右节点位置的值出现大于根位置的值，则交换
    if largest != rootindex:
        nums[largest], nums[rootindex] = nums[rootindex], nums[largest]
        buildheap(largest, nums, limit)  # 交换后，如果出现不满足大根堆的情况，则从这个节点开始再调整堆

# nums = [0,1,2,3,4,5,6]
# buildheap(0,nums,7)
# print(nums)


def heapsort(nums):
    # nums = [ 12, 11, 13, 5, 6, 7,2]
    size = len(nums)
    end = size // 2 - 1  # 在这个位置之后的元素，都是叶子结点

    # 对所有根节点操作，构建一个大根堆
    for rootindex in range(end, -1, -1): # 倒序
        buildheap(rootindex, nums, size)  # 注意这里用的也是size， 第一次排序是整个排序，所以限制limit==size

    k = len(nums) - 7 # 取数组的前 k 个最大值
    # 排序，通过调整 limit 剔除元素
    for i in range(size - 1, k, -1):
        nums[i], nums[0] = nums[0], nums[i]
        buildheap(0, nums, i)  # 这里的i作用是剔除元素，使其不参与堆结构的构建

    return nums
print(heapsort([0,1,2,3,4,5,6]))
```

#### 例题

1. ##### leetcode第88题，题目：合并两个有序数组，难度：简单

   **题目描述**

   给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。

   初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。你可以假设 nums1 的空间大小等于 m + n，这样它就有足够的空间保存来自 nums2 的元素。

   **题解**

   ```python
   class Solution:
       def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
           """
           Do not return anything, modify nums1 in-place instead.
           """
           # 创建一个第三方数组，
           sorted = []
           p1, p2 = 0, 0
           while True:
               if p1 == m:
                   sorted.extend(nums2[p2:])
                   break
               elif p2 == n:
                   sorted.extend(nums1[p1:])
                   break
               if nums1[p1] < nums2[p2]:
                   sorted.append(nums1[p1])
                   p1 += 1
               else:
                   sorted.append(nums2[p2])
                   p2 += 1
           nums1[:] = sorted[:m+n]
   ```

2. ##### LeetCode第121题，题目：买卖股票的最佳时机，难度：简单

   **题目简介**

   给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

   你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

   返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

   **题解**

   ```python
   class Solution:
       def maxProfit(self, prices: List[int]) -> int:
           if len(prices) <= 1:
               return 0
           # 记录遍历数组的最小值，和最大差值，
           minprice = float('inf')
           diff = 0
   
           for price in prices:
               minprice = min(minprice,price)
               diff = max(diff,price-minprice)
   
           return diff
   ```

3. ##### LeetCode第136题，题目：只出现一次的数字，难度：简单

   > 抑或的作用和他的性质
   >
   > xor   ^ 抑或   A，B有一个为真，但不同时为真，A==B时，为1，A!=B时，为0
   >
   > x^x=0，x^0=x
   >
   > 一个数和 0 做 XOR 运算等于本身：a⊕0 = a
   >
   > 一个数和其本身做 XOR 运算等于 0：a⊕a = 0
   >
   > XOR 运算满足**交换律和结合律**：a⊕b⊕a = (a⊕a)⊕b = 0⊕b = b

   **题目简介**

   给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

   说明：

   你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗

   **题解**

   ```python
   class Solution:
       def singleNumber(self, nums: List[int]) -> int:
           # 原位替换加标记
           # 标记指的是，不改变原来顺序，直接将其替换为一个特殊的值，如 object
           # size = len(nums)
   
           # if size == 1:
           #     return nums[0]
   
           # nums.sort()
   
           # for i in range(1, size):
           #     if nums[i] == nums[i - 1]:
           #         nums[i], nums[i - 1] = object, object
   
           # for num in nums:
           #     if num != object:
           #         return num
   
           # 抑或运算的性质
           # x^x=0，x^0=x
           # XOR 运算满足交换律和结合律：a⊕b⊕a = (a⊕a)⊕b = 0⊕b = b
           # ret = 0
           # for i in range(len(nums)):
           #     ret = ret ^ nums[i]
           # return ret
   ```

4. ##### LeetCode第283题，题目：移动零，难度：简单

   **题目描述**
   
   给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。
   
   **题解**
   
   ```python
   class Solution:
       def moveZeroes(self, nums: List[int]) -> None:
           """
           Do not return anything, modify nums in-place instead.
           """
           # 时间消耗太大了
           # 反向冒泡选择，再正向冒泡替换
           # for i in range(len(nums)-1,-1,-1):
           #     if not nums[i]:
           #         for j in range(i,len(nums)-1):
           #             nums[j],nums[j+1] = nums[j+1],nums[j]
   
           # 双指针
           # 设置一个循环不变量，在他之前的数组都是已排好序的，他之后的是待排序的
           loop_invariable = 0
           for i in range(len(nums)):
               if nums[i]:
                   nums[i],nums[loop_invariable] = nums[loop_invariable],nums[i]
                   loop_invariable += 1
   ```
   
5. ##### LeetCode第448题，题目：找到所有数组中消失的数字，难度：简单

   ```python
   class Solution:
       def findDisappearedNumbers(self, nums: List[int]) -> List[int]:
           
           # 标记法，主要用于不使用额外空间，找到某个元素
           for i in range(len(nums)):
               index = abs(nums[i])-1
               if nums[index]:
                   # 计算每一个下标，看这个下标是不是出现过，如 2 ，将2这个位置上的元素标记，表明2已经出现过了
                   nums[index] = -1 * abs(nums[index])
   
           ret = []
           for index,value in enumerate(nums):
               if value>0:
                   ret.append(index+1)
           return ret
   ```

6. ##### LeetCode第75题，题目：颜色分类，难度：简单

   **题目描述**

   给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

   此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

   **题解**

   ```python
   class Solution:
       def sortColors(self, nums: List[int]):
           """
           Do not return anything, modify nums in-place instead.
           """
           if len(nums) <= 1:
               return
           # 对于循环不变量的应用，设置两个不变量，zero,two,在zero之前的都是0，在two之后的都是2
           zero = 0
           i = 0
           two = len(nums)-1
           while i <= two:
               # 主要是这一部分，在交换之后，重新判断交换过来的当前值，所以i不能写在外面，他要随着其中一个不变量走
               if nums[i] == 0:
                   nums[zero],nums[i] = nums[i],nums[zero]
                   zero += 1
                   i += 1
               elif nums[i] == 1:
                   i += 1
               elif nums[i] == 2:
                   nums[two],nums[i] = nums[i],nums[two]
                   two -= 1
   ```

7. ##### LeetCode第26题，题目：删除有序数组中的重复项 难度：简单

   **题目描述**

   给你一个有序数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。

   不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

   **题解**

   ```python
   class Solution:
       # 这是一道典型的循环不变量的问题
       def removeDuplicates(self, nums: List[int]) -> int:
           if len(nums) <= 1:
               return len(nums)
           loop_invariant = 1
           for i in range(1,len(nums)):
               if nums[i] != nums[i-1]:
                   nums[loop_invariant] = nums[i]
                   loop_invariant += 1
           return loop_invariant
   ```
   
8. ##### LeetCode第33题，题目：搜索旋转排序数组，难度：中等

   **题目简介：**

   大概意思就是，一个升序的数组，从某一处截开，将后面一段放到前面，形成一个升降升的数组

   **题解**
   
   ```python
   class Solution:
       def search(self, nums: List[int], target: int) -> int:
           # 二分查找
           l, r = 0, len(nums) - 1
   
           while l <= r:
               mid = (l + r) // 2
               if nums[mid] == target:
                   return mid
               if l == r: # 这里是特殊处理
                   return -1
               # 这里要是 >= 
               if nums[mid] >= nums[l]:                 # 说明l - mid 的范围是有序的
                   if nums[l] <= target < nums[mid]:   # 如果在有序的一段，则正常的二分查找
                       r = mid - 1
                   else:                               # 如果不在有序的一段，则缩小范围，准备下次分段
                       l = mid + 1
               # 这里也要是 <= 都包含这个元素是为了只有两个元素的边界情况
               elif nums[mid] <= nums[r]:               # 说明 mid - r的一段是有序的
                   if nums[mid] < target <= nums[r]:   # 同样，如果在有序的一段，则正常的二分查找
                       l = mid + 1
                   else:                               # 同样，如果不在有序的一段，则缩小范围，准备下次分段
                       r = mid - 1
   
           return -1
   ```
   
9. ##### LeetCode第34题，题目： 在排序数组中查找元素的第一个和最后一个位置，难度：中等

   **题目简介**：

   给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

   如果数组中不存在目标值 target，返回 [-1, -1]。

   进阶：

   你可以设计并实现时间复杂度为 O(log n) 的算法解决此问题吗？

   **题解**

   ```python
   class Solution:
       def searchRange(self, nums: List[int], target: int) -> List[int]:
           if not nums:
               return [-1,-1]
   
           l,r = 0,len(nums)-1
   
           # 二分查找中 没有找到时，和 找到了数组中只有一个元素
           while l<=r:
               mid = (l+r)//2
   
               if nums[mid] == target:
                   l,r = mid-1,mid+1
                   while l>=0 and nums[l] == target:
                       l -= 1
                   while r < len(nums) and nums[r] == target:
                       r += 1
                   return [l+1,r-1]
   
               elif nums[mid] > target:
                   r = mid-1
               else:
                   l = mid+1
   
           return [-1,-1]
   s = Solution()
   nums = [1,3]
   target = 3
   # nums = [2,2]
   # target = 2
   print(s.searchRange(nums,target))
   ```

10. ##### LeetCode第162. 题，题目：寻找峰值，难度：中等

    **题目简介：**

    峰值元素是指其值大于左右相邻值的元素。

    给你一个输入数组 nums，找到峰值元素并返回其索引。数组可能包含多个峰值，在这种情况下，返回 任何一个峰值 所在位置即可。

    你可以假设 nums[-1] = nums[n] = -∞ 。

    **题解**

    ```python 
    class Solution:
        # 迭代二分查找
        def findPeakElement(self, nums: List[int]) -> int:
            if len(nums) == 1:
                return 0
    
            l, r = 0, len(nums) - 1
    
            while l<=r:
                mid = (l + r) // 2
    
                left = float("-inf") if mid - 1 < 0 else nums[mid - 1]
                right = float("-inf") if mid + 1 >= len(nums) else nums[mid + 1]
    
                if left < nums[mid] and nums[mid] > right:
                    return mid
                elif nums[mid] <= right:
                    l = mid + 1
                elif nums[mid] <= left:
                    r = mid - 1
            return mid
    
    
    tests = [
        [1,2,3,1],
        [1,2,1,3,5,6,4],
        [1],
        [1,2],
        [0,0,1,1,0,0,0]]
    
    s = Solution()
    for test in tests:
        print(s.findPeakElement(test))
    ```

11. ##### LeetCode第240. 题，题目：搜索二维矩阵 II，难度：中等

    **题目简介：**

    编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target 。该矩阵具有以下特性：
    
    每行的元素从左到右升序排列。
    每列的元素从上到下升序排列。
    
    **题解**
    
    ```python
    class Solution:
        def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
            # 寻找特殊性，所谓特殊性就是最大，最小
            left,down = 0,len(matrix)-1
    
            while down>=0 and left < len(matrix[0]):
                if matrix[down][left] == target:
                    return True
                
                elif matrix[down][left] > target:
                    down -= 1
                else:
                    left += 1
    
            return False
    ```
    
12. LeetCode第题，题目：，难度：中等

    题目简介：

    题解
