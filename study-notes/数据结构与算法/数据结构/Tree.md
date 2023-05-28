### 	树

> 又叫堆

**三种遍历方式**

1. 先序遍历：遍历顺序规则为【根左右】[^先返回根在返回左右]

2. 中序遍历：遍历顺序规则为【左根右】[^先返回左再返回根右]

   > 可以得到一条顺序的列表，（顺序列表转化为中序遍历二叉树，再转回来）

3. 后序遍历：遍历顺序规则为【左右根】

#### 定义

节点：**结点**是数据结构中的基础，是构成复杂数据结构的基本组成单位。

度：结点拥有的子树数目称为结点的**度**。

#### 二叉树的性质

1. 二叉树的第 i 层最多有 2[^i-1]节点

2. 如果二叉树的深度为 k ，那么最多有 2[^k]-1个节点

3. n0=n2+1 n0表示度数为0（叶子节点）的节点数，n2表示度数为2的节点数

   这个意思是说，

4. 在完全二叉树中，具有n个节点的完全二叉树的深度为[log2n]+1，其中[log2n]是向下取整。

5. 若对含 n 个结点的完全二叉树从上到下且从左至右进行 1 至 n 的编号，则对完全二叉树中任意一个编号为 i 的结点有如下特性：（**根据这个性质构建堆排序**）

   -  若 i=1，则该结点是二叉树的根，无双亲, 否则，编号为 [i/2] 的结点为其双亲结点;
   - 若 2i>n，则该结点无左孩子，  否则，编号为 2i 的结点为其左孩子结点；
   - 若 2i+1>n，则该结点无右孩子结点，  否则，编号为2i+1 的结点为其右孩子结点。



#### 二叉搜索树（BST）

> 树的根节点大于所有的左子孙节点，小于所有的右子孙节点，

- 节点的左子树只包含**小于**当前节点的数。
- 节点的右子树只包含**大于**当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。

**特点：**二叉搜索树通过中序遍历，会得到一条升序的序列

#### 完全二叉树

> 所有节点尽量填满树的每一层，还有剩余节点的话，从左往右尽量填满下一层
>
> 大根堆，每个根节点的值大于或等于它的左右节点
>
> 小根堆，每个根节点的值小于或等于它的左右节点

完全二叉树中树的深度与节点个数的关系为：![n=2^{d+1}-1](https://math.jianshu.com/math?formula=n%3D2%5E%7Bd%2B1%7D-1)

d 是完全二叉树的深度，即查找次数（由于二叉树的查询，插入，删除效率都与树的深度有关，所以使树尽量平衡，能提升效率

单个节点的构造复杂度和查询复杂度相同，为 ![O(log_2 n)](https://math.jianshu.com/math?formula=O(log_2%20n))~![O(n)](https://math.jianshu.com/math?formula=O(n))

#### 平衡二叉树（AVL）红黑树

> 1. 可以是空树。
> 2. 假如不是空树，任何一个结点的左子树与右子树都是平衡二叉树，并且高度之差的绝对值不超过 1

平衡二叉树的调节方式，左旋，右旋

[参考一](https://zhuanlan.zhihu.com/p/56066942)

#### B树

![B树](C:\Users\24781\Desktop\学习笔记\resource\B树.png)

**特点：**

- 不是二叉树，是n叉树
- 叶子结点，非叶子结点，都存储数据
- 中序遍历，可以获得所有节点

#### 使用列表构造一棵二叉树

> 此树仅用于个人LeetCode二叉树解题调试，且仅满足部分格式，个人构造，目前来看应该使用队列优化，且功能不多，后续在树的学习过程中再改造
>
> 支持的方法包括：三种遍历方法，扁平化，单独添加，集体添加

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

    def add(self, val, lr=1):
        # 二叉树的单独添加，默认lr=1,即添加到左叶，若lr=0，添加到右叶
        node = TreeNode(val)
        if lr:
            self.left = node
        else:
            self.right = node

    @staticmethod
    def adds(nodes: list):
        # 二叉树的集体添加，实际上就是一个层序遍历，广度优先
        root = TreeNode(nodes[0])
        stack = [root]
        i = 1
        while stack and i < len(nodes):
            size = len(stack)
            for _ in range(size):
                node = stack.pop(0)
                if i < len(nodes) and nodes[i] != "null":
                    node.left = TreeNode(nodes[i])
                    stack.append(node.left)
                if i + 1 < len(nodes) and nodes[i + 1] != "null":
                    node.right = TreeNode(nodes[i + 1])
                    stack.append(node.right)
                i += 2
        return root

    @staticmethod
    def flat(root):
        # 广度优先展开，使用层序遍历
        stack = [root]
        queue = []
        while stack:
            size = len(stack)
            for _ in range(size):
                node = stack.pop(0)
                if not node:
                    queue.append("null")
                else:
                    queue.append(node.val)  # 值放在队列中
                    stack.append(node.left)  # 节点放在栈中
                    stack.append(node.right)
        while queue[-1] == "null": queue.pop()
        return queue

    def traversal(self, type=1, method=1):
        """
        type:为使用方法 1:递归，2:迭代
        method: 为遍历方式，1:根左右(先序遍历),2:左根右(中序遍历),3:左右根(后序遍历)
        """
        # 递归
        if type==1:
            ret = []
            def dfs(root):
                if not root:
                    return
                if method==1: # 先序遍历
                    ret.append(root.val)
                dfs(root.left)
                if method==2: # 中序遍历
                    ret.append(root.val)
                dfs(root.right)
                if method==3: # 后序遍历
                    ret.append(root.val)
            dfs(self)
            return ret
        # 颜色标记法
        elif type==2:
            WHITE, GRAY = 1, 0
            stack = [(self, WHITE)]
            queue = []
            while stack:
                node, color = stack.pop()
                if not node: continue
                if color == WHITE:
                    if method == 1: # 先序遍历
                        stack.extend([(node.right, WHITE), (node.left, WHITE), (node, GRAY)])
                    elif method == 2: # 中序遍历
                        stack.extend([(node.right, WHITE), (node, GRAY), (node.left, WHITE)])
                    elif method == 3: # 后序遍历
                        stack.extend([(node, GRAY), (node.right, WHITE), (node.left, WHITE)])
                else:
                    queue.append(node.val)
            return queue
```

### 一种通用的遍历方法

> 广度优先遍历

```python
# 颜色标记法
class Solution1:
    def inorderTraversal(self, root):
        WHITE, GRAY = 0, 1
        res = []
        stack = [(WHITE, root)]
        while stack:
            color, node = stack.pop()
            if node is None: continue
            if color == WHITE:
                stack.append((WHITE, node.right))
                stack.append((GRAY, node))
                stack.append((WHITE, node.left))
            else:
                res.append(node.val)
        return res

# 类型标记法，left,right为 TreeNode类型，val 为 Int类型
class Solution2:
    def inorderTraversal(self, root):
        stack, rst = [root], []
        while stack:
            i = stack.pop()
            if isinstance(i, TreeNode):
                stack.extend([i.right, i.val, i.left])
            elif isinstance(i, int):
                rst.append(i)
        return rst  
```

### 例题

LeetCode第112题，题目：路径总和，难度：简单

**题目描述**

给你二叉树的根节点 root 和一个表示目标和的整数 targetSum ，判断该树中是否存在 根节点到叶子节点 的路径，这条路径上所有节点值相加等于目标和 targetSum 。

**题解**

```python
class Solution:
    # 标准的深度优先遍历，
    # 确定返回值，本题是 bool，
    # 确定返回条件
    # 编写操作方法
    def hasPathSum(self, root: TreeNode, targetsum: int) -> bool:
        if not root:
            return False
        if not root.left and not root.right:
            return targetsum == root.val
        targetsum -= root.val
        return self.hasPathSum(root.left, targetsum) or self.hasPathSum(root.right, targetsum)
```



**1. LeetCode第101题，题目：对称二叉树，难度：简单**

**题目简述**

给定一个二叉树，检查它是否是镜像对称的

```
  例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

**题解一，深度优先遍历**

> 深度优先遍历，常使用递归，一条道走到黑

```python
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        # 同时开启两个指针，分别遍历左右子树
        if not root:
            return True
        def dfs(left,right):
            """深度优先遍历左右子树，判断左右两个节点是否对称"""
            # 判断（0,0）情况，左右子树同时为空
            if not (left or right):
                return True
            # 判断(0,0),(0,1),(1,0),其中（0,0）情况已经被过滤，左右子树有一方为空
            if not (left and right):
                return False
            # 判断左右子树的值是否相同
            if left.val != right.val:
                return False
            return dfs(left.left,right.right) and dfs(left.right,right.left)
        return dfs(root.left,root.right)
```

**题解二，广度优先遍历**

> 广度优先遍历，需要用到队列，一边进，一边出，才能不陷入循环，顺序水平遍历

```python
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        if not root:
            return True
        queue = [(root.left,root.right)]
        while queue:
            left,right = queue.pop(0)
            # 判断（0,0）情况，左右子树同时为空 则继续循环
            if not (left or right):
                continue
            # 判断(0,0),(0,1),(1,0),其中（0,0）情况已经被过滤，左右子树有一方为空
            if not (left and right):
                return False
            # 判断左右子树的值是否相同
            if left.val != right.val:
                return False
            queue.append((left.left,right.right))
            queue.append((left.right,right.left))
        return True
```

2. **LeetCode第104题，题目：二叉树的最大深度，难度 ：简单**

   **题目描述：**

   给定一个二叉树，找出其最大深度。

   二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

   说明: 叶子节点是指没有子节点的节点。

   示例：
   给定二叉树 [3,9,20,null,null,15,7]，

   ```
       3
      / \
     9  20
       /  \
      15   7  返回它的最大深度 3 。
   ```

   **题解**一，迭代求解，广度优先遍历

   ```python
   """从当前层队列中弹出元素，将这个元素的子节点添加到下一层列表中，当前层队列为空时，将当前层队列更新为下一层列表，直到到达最底层"""
   class Solution:
       def maxDepth(self, root: TreeNode) -> int:
           if not root:
               return 0
           ret = 0
           # 创建一个当前层队列
           queue = [root]
           while queue:
               ret += 1
               # 创建一个下一层列表
               stack = []
               # 当前层队列为空时，将下一层列表附加为当前层队列
               while queue:
                   node = queue.pop()
                   # 如果node不存在，或它的左右节点都不存在
                   if not node or not (node.left or node.right):
                       continue
                   stack.append(node.left)
                   stack.append(node.right)
               queue.extend(stack)
           return ret
   ```

   **题解二**,递归，深度优先遍历

   ```python
   class Solution:
       def maxDepth(self, root: TreeNode) -> int:
           if not root:
               return 0
   
           def bfs(root,ret):
               if not root:
                   return ret
               left = bfs(root.left,ret+1)
               right = bfs(root.right,ret+1)
               return max(left,right)
           return bfs(root,0)
   ```

3. **LeetCode第617题，题目：合并二叉树，难度：简单**

   **题目描述**

   给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

   你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点

   **题解**一，递归解法

   ```python
   class Solution:
       # 写递归之前，先确定要传入的参数，要返回的值，以及运算条件
       def mergeTrees(self, root1: TreeNode, root2: TreeNode) -> TreeNode:
           # 本题传入两个节点，返回一个节点
           if not root1:
               return root2
           if not root2:
               return root1
           node = TreeNode(root1.val+root2.val)
           node.left = self.mergeTrees(root1.left,root2.left)
           node.right = self.mergeTrees(root1.right,root2.right)
           return node
   ```

4. **LeetCode第226题，题目：翻转二叉树，难度：简单**

   **题目描述**

   **题解一** 深度优先遍历

   ```python
   class Solution:
       # 很正统的写法，确定结束条件，执行运算操作，返回值
       # 要反转一颗二叉树，首先需要保证这个节点不为空，然后翻转它的左右节点，之后就是循环判断翻转，最后返回
       def invertTree(self, root: TreeNode) -> TreeNode:
           if not root:
               return None
           root.left, root.right = root.right, root.left #中
           self.invertTree(root.left) #左
           self.invertTree(root.right) #右
           return root
   ```

   **题解二**

   ```python
   class Solution:
       def invertTree(self, root: TreeNode) -> TreeNode:
           if not root:
               return root
           st = [root]
           while st:
               node = st.pop()
               node.left, node.right = node.right, node.left #中
               if node.right:
                   st.append(node.right) #右
               if node.left:
                   st.append(node.left) #左
           return root
   ```
   

**题解三**

```python
   import collections
   class Solution:
       def invertTree(self, root: TreeNode) -> TreeNode:
           queue = collections.deque() #使用deque()
           if root:
               queue.append(root)
           while queue:
               size = len(queue)
               # 这个和二叉树的最大深度的题解一，类似，没有使用一个列表，直接用一个数字，来表示本层节点的数量
               for i in range(size):
                   node = queue.popleft()
                   node.left, node.right = node.right, node.left #节点处理
                   if node.left:
                       queue.append(node.left)
                   if node.right:
                       queue.append(node.right)
           return root
```



5. **LeetCode第102题，题目：二叉树的层序遍历，难度：中等**

   **题目描述**

   **题解**

   ```python
   class Solution:
       """广度优先遍历，需要一个队列，"""
       def levelOrder(self, root: TreeNode) -> List[List[int]]:
           stack = [root]
           ret = []
           while stack:
               # 这个外层遍历的是深度
               nodes = []
               size = len(stack)
               for _ in range(size):
                   # 这个内层遍历的是宽度（每一层的节点数）
                   node = stack.pop(0)
                   if not node:
                       continue
                   nodes.append(node.val)
                   stack.append(node.left)
                   stack.append(node.right)
               if nodes:
                   ret.append(nodes)
           return ret
   ```

6. **LeetCode第534题，题目：二叉树的直径，难度：简单**

   **题目描述**

   给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是**任意两个结点路径长度**中的最大值。这条路径可能穿过也可能不穿过根结点。

   ```
             1
            / \
           2   3
          / \     
         4   5    宽度为4, 1的leftdeepth+rightdeepth 大于其他所有节点
   ```

   **题解，深度优先搜索**

   ```python
   class Solution:
       def __init__(self):
           self.max = 0
       def diameterOfBinaryTree(self, root: TreeNode) -> int:
           self.ans = 1
           def depth(node):
               if not node:
                   return 0
               
               L = depth(node.left)
               R = depth(node.right)
               
               self.ans = max(self.ans, L + R + 1)
               return max(L, R) + 1
           
           depth(root)
           return self.ans - 1
   
           
           def deepth(root):
               """
               这个函数的目标是获取从本节点开始的最大深度，但是在遍历的同时，会获取到每一个节点的左最大深度和右最大深度，
               每一个节点的左最大深度和有最大深度之和-1 是这个节点的宽度，self.max是一个全局变量，他负责记录这个所有节点宽度的最大值
               """
               if not root:
                   return 0
               left = deepth(root.left) + 1
               right = deepth(root.right) + 1
               D = left+right-1
   
               self.max = max(D,self.max)
               return max(left,right)
           deepth(root)
           return self.max
   ```

7. **剑指offer -II. 题目：二叉树的最近公共祖先 ，难度：简单**

   **题目描述**

   给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

   百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。

   **题解**

   ```python
   # 题目描述说找到两个指定节点的最近公共祖先，那么要做的就是找到每一个从根节点到指定节点的最短路径，那么他们# 重叠的部分就是他们的公共祖先，然后从公共祖先中找出最近的就简单了，本题中的列表应该替换成队列
   class Solution:
           def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
           # 递归求解
           if not root:
               return 
           if root == q or root == p:
               return root
           
           left = self.lowestCommonAncestor(root.left,p,q)
           right = self.lowestCommonAncestor(root.right,p,q)
   
           if left and right:
               return root
           elif left:
               return left
           elif right:
               return right
           else:
               return
           
       def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
           def shortstpathfromroot(root, target):
               stack = [root]  # 存储每一层的所有元素
               queue = []      # 存储所有走过的路径，最后一个元素是离当前元素最近的历史路径
               ret = []        # 从所有历史路径中选出最短路径
               while stack:
                   size = len(stack)
                   for _ in range(size):
                       node = stack.pop(0)
                       if not node: continue
                       if node.val == target.val: # 如果发现了目标元素，就从历史元素中查找当前元素的父元素，再找父元素的父元素，知道根节点
                           while queue:
                               prenode = queue.pop()
                               if prenode.left == node or prenode.right == node: # 筛选出最近的路径
                                   ret.append(prenode)
                                   node = prenode
                       else:
                           queue.append(node)
                           stack.append(node.left)
                           stack.append(node.right)
               # return [node.val for node in ret]
               return ret
           pstack = shortstpathfromroot(root,p) # p节点到根节点的最短路径
           qstack = shortstpathfromroot(root,q) # q节点到根节点的最短路径
   
           # 这里将两个目标节点插入路径中，因为题目要求，节点可以是自己的祖先
           pstack.insert(0, p)
           qstack.insert(0, q)
   
           # 已找到两个指定点的最短路径，存储最短路径多经过的节点，选出最近父节点
           for i in pstack:
               for j in qstack:
                   if i.val == j.val:
                       return i
   
   ```

#### 参考

1. [二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/solution/yi-pian-wen-zhang-dai-ni-chi-tou-dui-che-asg8/)