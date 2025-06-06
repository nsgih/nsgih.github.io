---
layout: post
title: alg complex
date: 2025-05-14 23:43 +0800
tag: complex
---

[![Static Badge](https://img.shields.io/badge/%E7%81%B5%E7%A5%9E-%E7%A7%91%E5%AD%A6%E5%88%B7%E9%A2%98-55acee?logo=leetcode&logoColor=%23FFA116)](https://leetcode.cn/discuss/post/3141566/ru-he-ke-xue-shua-ti-by-endlesscheng-q3yd/)
[![Static Badge](https://img.shields.io/badge/{num}-%E7%81%B5%E7%A5%9E-55acee?logo=leetcode&logoColor=%23FFA116)](#)
[![Static Badge](https://img.shields.io/badge/%E9%A2%98%E8%A7%A3-nagi-55acee?logo=leetcode)](https://leetcode.cn/u/silly-swartzd9f/)


[![alt text](/assets/2025-05/image-1.png)](https://leetcode.cn/discuss/post/3584387/fen-xiang-gun-mo-yun-suan-de-shi-jie-dan-7xgu/)_python每秒执行10e7运算\模运算_

## 脚手架

### 图论



### xor and or

```python
# 用or实现xor
P or P # inclusive
(P or Q) and not (P and Q) # exclusive
```

#### 对称加密

```python
# plaintext, key, ciphertext 加密,
p,k = "hi there", 27

# 1.凯撒加密，偏移加密
def caesar_cipher(p,shift):
    # 也可以用ch.isupper()/.islower()去条件分支
    return ''.join(chr(((ord(char)-65)+shift)%26+65) for char in p.upper())

# 2. xor加密
def xor_cipher(plaintext,key):
    # 加密
    # c=p^k

    # 解密
    # p=c^k=p^k^k
    # =p^0 /xor反自反，亦即满足k^k=0； FYI 自反指的是k^k=k这里不成立
    # =p ./xor的零元0，亦即满足任意k^0===k
    return ''.join(chr(ord(char)^key) for char in plaintext)
```

### 排列、组合
62@不同路径
```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        # @factorial,permutation,combination
        # 排列perm(n,c)=全排列n//1//排列n-c
        # 组合comb(n,c)=全排列n//排列c//排列n-c
        # 说明: n全排列方案数=c排列方案数*(n-c)排列方案数*(n,c)组合方案数
        # math.factorial(n)
        # math.perm(a,b)
        # math.comb(a,b)
        return math.comb(m+n-2,m-1)

        # @dp
        f=[[0]*(n+1) for _ in range(m+1)]
        f[0][1]=1
        for i in range(1,m+1):
            for j in range(1,n+1):
                f[i][j]=sum(f[i+dx][j+dy] for dx,dy in [(-1,0),(0,-1)])
        
        return f[-1][-1]

        # @dp@空间优化
        f=[0]*(n+1)
        f[1]=1
        for _ in range(m):
            for j in range(n):
                #                            ^^  ^^
                #                         上轮f[j+1] 本轮f[j]
                f[j+1]=sum(f[j+1+dx] for dx in [0,-1])
        
        return f[-1]
```

### 二分

@3356II

| 区间类型                  | 初始范围            | 循环条件    | 更新方式                    |
| ------------------------- | ------------------- | ----------- | --------------------------- |
| **左闭右闭 `[l, r]`**     | `l=0, r=n-1`        | `l <= r`    | `l = mid + 1 / r = mid - 1` |
| **左闭右开 `[l, r)`**     | `l=0, r=n`          | `l < r`     | `l = mid + 1 / r = mid`     |
| **两端开区间 `(l, r)`** ✅ | `l = -1, r = n + 1` | `l + 1 < r` | `r = mid / l = mid`         |

```python
# (l,r)
# 双开直接更新为mid，开区间不断丢包
left, right = -1, q + 1        # 初始化为超出范围的边界
while left + 1 < right:        # 保证区间至少还有一个候选
    mid = (left + right) // 2
    if check(mid):             
        right = mid            # 答案在左边，收缩右边界（仍然保留 mid）
    else:
        left = mid             # 答案在右边，收缩左边界
return right if right<=q else -1

# [l,r)
# 单开闭区间一端更新为mid+1
l, r = 0, n  # 注意 r = n
while l < r:
    mid = (l + r) // 2
    if check(mid):  # mid 满足条件，可能是答案
        r = mid     # 缩小右边界（仍然保留 mid）
    else:
        l = mid + 1 # mid 不满足，丢弃 mid
return l if l<=q else -1

# [l,r]
# 双闭直接l,r = mid+1,r=mid-1
# 当check()==True则在right中保留mid
l, r = 0, n - 1
res = -1  # 用来记录答案
while l <= r:
    mid = (l + r) // 2
    if check(mid):     # mid 满足，保留答案
        res = mid      # 或直接 return mid，如果找第一个满足的即可
        r = mid - 1    # 向左边继续找
    else:
        l = mid + 1
return res if res<=q else -1
```
#### Q：返回l|r|res？

引入循环不变量分析视角。
```python
# 全开 退出时 left + 1 == right
(-∞ … -1]     (left)  (right)     [q+1 … +∞)
    全 False    ?      全 True

# 左开右闭 循环终止条件是 l == r，此时区间空
 已判定    [l, r) 待判定
全 False     ?        全 True

# 全闭 循环退出条件 l > r 时，l 指向“第一个 可能为 True 的位置”
# 但如果根本没有 True，l 会超出右端；
# 如果在过程中把 mid 丢过头，也会导致 l 越过答案。
# res 保存了“最后一次见到的 True”，才是可靠答案
[l,       mid-1] mid [mid+1,       r]
   未知             ✓/✗            未知
```

于是：

| 模板              | 不变量里“答案”停留的位置 | 退出时哪一指针一定指向答案 | 是否需要额外变量 |
| ----------------- | ------------------------ | -------------------------- | ---------------- |
| `(l, r)` 两端开   | `right` 侧               | `right`                    | 否               |
| `[l, r)` 左闭右开 | 两指针重合时的位置       | `l` (=`r`)                 | 否               |
| `[l, r]` 左闭右闭 | 可能被边界跨过           | 不保证，需要 `res`         | 是               |

#### l,r双指针维护的是可行解区间吗？

并非如此：

| 区间类型 | 区间表示的含义         | 是否是“可行解区间”   |
| -------- | ---------------------- | -------------------- |
| `(l, r)` | 候选解区间（不含端点） | ❌ 含可行解，但不全是 |
| `[l, r)` | 未知区间 / 未排除区间  | ❌ 可能混合 F 和 T    |
| `[l, r]` | 搜索过程区间           | ❌ 可能混合 F 和 T    |
| `res`    | 最后存下来的合法解     | ✅ 是可行解之一       |

### 线段树

维护区间信息的数据类型，在Ologn实现单点修改、区间修改、区间查询（求和、最大值、最小值）等操作。

**普通线段树(Segement Tree，下称ST)**，依照维护属性不同分为区间和ST、区间最大值ST、区间最小值ST、区间乘积ST、区间GCD的ST、区间异或的ST。支持区间查询[l,r]的和、最值，单点修改a[i]修改。

**带懒标记的线段树（Lazy ST）**则是用标记延迟更新。

```python
O_Create=On
O_Read_or_Updare=Ologn
```

### [排序sort](https://visualgo.net/zh/sorting)*

- 快排
```python
def sort(arr):
    if len(arr)<=1:
        return arr

    pivot = arr[0]
    left=[x for x in arr[1:] if x<=pivot]
    right=[x for x in arr[1:] if x>pivot]
    return sort(left)+[pivot]+sort(right)
```
### python

### 字典dict

```python
# 对应打包生成字典mp
mp=dict(zip(chars,vals))  
# mp.get(c,default_value) 要么得到字典序，要么返回默认值
mp.get(c,ord(c)-ord('a')+1) 

# python@3.9 覆盖写法
mp=dict(zip(ascii_lowercase,range(1,27))) | dict(zip(chars,vals))

# chars, val eg.
chars="abc"
val=[-1,-1,-1]
```

#### 网格数组、多维数组

```python
# 一维数组
[1]*2 = [1,1] # *是复制引用，对不可变对象例如1，2，3而不是可变对象a,b,c来说没问题
[1 for _ in range(2)]=[1,1] # list comprehension不存在引用共享问题，每次都创建新对象

# 二维数组
[1,1]*2=[1,1,1,1] 
[[1,1] for _ in range(2)]=[[1,1],[1,1]]

f=[[inf for _ in range(n+1)] for _ in range(m+1)]
f=[[inf]*(n+1) for _ in range(m+1)]
# f=[[[inf]*(n+1)] *(m+1)]

# 三维数组
[[[0]*x for _ in range(y)] for _ in range(z)]
```

#### list comprehension

> The word “comprehension” comes from the Latin comprehendere, meaning “to grasp,” “to include,” or “to take in.”
> 
> So in programming, a comprehension is a compact way to construct (or "grasp") a new collection from an existing one.
> 
> “Comprehension” means:
> 
> Grabbing and shaping data from something else
> using a compact expression.
>
> - Grasping values from an iterable (for x in ...)
> 
> - Filtering them optionally (if ...)
> 
> - Transforming them (expression)
> 
> - Composing a new collection (list, set, dict)
{: .prompt-info}

### [地图](https://leetcode.cn/discuss/post/3141566/ru-he-ke-xue-shua-ti-by-endlesscheng-q3yd/)
计划首先每日一题+周赛 --> 之后Hot100 --> 最后按照map刷。

### 区间
```python
[a,b].elem = |b-a| + 1 = b-a+1

[a,b).elem = |b-a| + 0 = b-a
(a,b].elem = |b-a| + 0 = b-a

(a,b).elem = |b-a| + -1 = b-a-1
```
### dp

子问题、状态定义、转移方程


## easy

121买卖股票的最佳时期@贪心，维护买入最小值
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        # ans=0
        # for i,x in enumerate(prices):
        #     for j in range(i+1,len(prices)):
        #         ans=max(ans,prices[j]-x)   
        # return ans
        
        ans=0
        local=prices[0] # 局部最小
        for p in prices:
            ans=max(ans, p-local) # 先更新答案、全局最大的盈利结果
            local=min(local, p) # 再更新左侧元素最小值、局部最小的买入价格
        return ans
```

746@dp,pairwise,
```python
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        # # len(cost):= 2..1000 说明终点位置是下标len(cost)地方
        # # 从0到i最小花费(不包括终点)
        # @cache
        # def dfs(i:int)->int:
        #     if i==0: # 累计路费0
        #         return 0
        #     if i==1: # 累计路费0
        #         return 0
    
        #     if i==2: # 边界
        #         return min(cost[:2])
            
        #     return min(dfs(i-1)+cost[i-1],dfs(i-2)+cost[i-2])
        
        # return dfs(len(cost))

        # f=[0]*(len(cost)+1)
        # for i in range(2,len(f)):
        #     f[i]=min(f[i-1]+cost[i-1],f[i-2]+cost[i-2])
        # return f[-1]

        # # @空间优化，注意写成range(1,nc)的情况
        # nc=len(cost)
        # f0=f1=0
        # for i in range(2,nc+1): # 改成1,n也可以,循环语句相应也要改就是
        #     new_f = min(f1+cost[i-1],f0+cost[i-2])
        #     f0=f1
        #     f1=new_f
        # return f1

        # @c0,c1 in pairwise写法
        f0=f1=0
        for (c0,c1) in pairwise(cost):
            f0,f1 = f1, min(f1+c1,f0+c0)
        return f1
```

70爬楼梯@dp,dfs,down-to-up,cache
```python
class Solution:
    def climbStairs(self, n: int) -> int:
        # # deep first search
        # # 定义为从0到i所有方法数目和
        # # 边界是dfs(0)==dfs(1)==1
        # def dfs(i:int)->int:
        #     if i<=1: # 边界
        #         return 1
        #     return dfs(i-1)+dfs(i-2)
        # return dfs(n)

        # # @dfs,cache
        # # dp时间复杂度=状态个数*单个状态计算时间=O(n*1)=On
        # @cache
        # def dfs(i:int)->int:
        #     if i<=1:
        #         return 1
        #     return dfs(i-1)+dfs(i-2)
        # return dfs(n)

        # # @down-to-up
        # # f[]数学等价于dfs()，只不过这边用递推
        # f=[0]*(n+1)
        # f[0]=f[1]=1
        # for i in range(2,n+1):
        #     # f2=f1+f0 2,1,0
        #     # f3=f2+f1 3,2,1
        #     # f4=f3+f2 4,3,2
        #     # f5=f4+f3 5,4,3
        #     f[i]=f[i-1]+f[i-2]
        # return f[n]

        # @优化空间意义不大
        f0=f1=1
        for _ in range(2,n+1):
            new_f = f0+f1
            f0=f1
            f1=new_f
        return f1
```

3024@三角不等式($abs(a-b)<c<a+b$)，tuple，set，哈希
```python
class Solution:
    def triangleType(self, nums: List[int]) -> str:
        # 对于a>b>c 三角不等式只剩下b+c>a非平凡
        nums.sort()
        a,b,c = nums
        if a+b<=c:
            return "none"
        if a==c:
            return "equilateral"
        if a==b or b==c:
            return "isosceles"
        return "scalene"

        # 哈希表，元素c=1等边，c=2等腰，c=3不等边
        nums.sort()
        if nums[0]+nums[1]<=nums[2]: 
            return "none"
        # tuple有序，不可变；set去重
        return ("equilateral","isosceles","scalene")[len(set(nums))-1]

```

[75](https://leetcode.cn/problems/sort-colors/?envType=daily-question&envId=2025-05-17)@排序，插入排序，冒泡排序，快速排序

```python
class Solution:
    def sortColors(self, nums: List[int]) -> None:
        # 插入排序O(n2)
        for i in range(1,len(nums)):
            key = nums[i]

            j=i-1
            while j>=0 and nums[j]>key: 
                nums[j+1],nums[j]=nums[j],nums[j+1]
                j-=1
                p0 = p1 = 0
        # 双指针优化O(n)
        for i, x in enumerate(nums):
            nums[i] = 2
            if x <= 1:
                nums[p1] = 1
                p1 += 1
            if x == 0:
                nums[p0] = 0
                p0 += 1
```

daily-2900@python,.append(x),.insert(0,x),.pop(x),.pop(0,x),.extend(other)
```python
class Solution:
    def getLongestSubsequence(self, words: List[str], groups: List[int]) -> List[str]:
        # how to traverse
        ans=[]
        ans.append(words[0]) # python append
        for i in range(len(groups)-1):
            if groups[i]^groups[i+1]==1:
                ans.append(words[i+1])
        return ans

        # or traverse by i,g in enumerate(groups)
        ans = []
        for i, g in enumerate(groups):
            if i == len(groups) - 1 or g != groups[i + 1]:  # i 是连续相同段的末尾
                ans.append(words[i])
        return ans

        # or groupby
from itertools import groupby
    # key=lambda z:z[1] -> groupby using group
    # zip-> [('a',1),('b',0),('c',1),('d',1)]
    # groupby(,key) -> [('a',1)], [('b',0)], [('c',1),('d',1)]
    groupby(zip(words, groups))
    groupby(zip(words, groups),key=lambda z:z[1])
    for _, g in groupby(zip(words, groups), key=lambda z: z[1])
    # next() -> Take only the first item from the group
    # next(g)[0] first item's char like words
    # [for] is List Comprehension
    next(g)[0] for _, g in groupby(zip(words, groups), key=lambda z: z[1])
    return [next(g)[0] for _, g in groupby(zip(words, groups), key=lambda z: z[1])]
```
## medium
3372@闭包，邻接表，暴力@直径优化
```python
class Solution:
    def build_tree(self, edges: List[List[int]], k: int) -> Callable[[int, int, int], int]:
        # @闭包@邻接表
        
        # 1. 邻接表g @邻接表@邻接矩阵@边列表
        g=[[] for _ in range(len(edges)+1)]
        for x,y in edges:
            g[x].append(y)
            g[y].append(x)
        
        # x,fa,d: cur node, father node, distance from node i
        # cnt: 
        def dfs(x,fa,d)->int:
            if d>k: # constraint: d <=k
                return 0
            cnt=1
            # adjacement table: 找x邻边
            for y in g[x]:
                if y!=fa: # 排除fa
                    cnt+=dfs(y,x,d+1) # 走远加一 
            return cnt # 返回node x's adjace side总数


        # 闭包要件:
        #   嵌套函数
        #   自由变量, 内部函数必须引用外部函数的变量
        #   “冻结”其作用域, 对外部函数返回这个内部函数
        return dfs

    def maxTargetNodes(self, edges1: List[List[int]], edges2: List[List[int]], k: int) -> List[int]:
        # @local 
        # edges2的最优埠节点、局部最优节点 
        max2=0
        if k:
            dfs=self.build_tree(edges2,k-1) # 第一次宣告dfs
            max2=max(dfs(i,-1,0) for i in range(len(edges2)+1)) # V=E+1
        
        # 闭包 closure
        # 第二次宣告dfs 
        dfs=self.build_tree(edges1,k)
        
        print(dfs)
        # @gobal
        # 枚举edges1达到全局最优
        return [dfs(i,-1,0)+max2 for i in range(len(edges1)+1)] # V=E+1
```

63不同路径II@空间优化::原地修改，逻辑操作
```python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        # @递推recurrence
        m,n=len(obstacleGrid),len(obstacleGrid[0])
        f=[[0]*(n+1) for _ in range(m+1)]
        f[0][1]=1
        for i in range(m):
            for j in range(n):
                f[i+1][j+1]=sum(0 if obstacleGrid[i][j] else f[i+1+dx][j+1+dy] for dx,dy in [(0,-1),(-1,0)] )
        return f[-1][-1]

        # @递推recurrence，空间优化
        f=[0]*(n+1)
        f[1]=0 if obstacleGrid[0][0] else 1
        for i in range(m):
            for j in range(n):
                f[j+1]=sum(0 if obstacleGrid[i][j] else f[j+1+dx] for dx in [0,-1])
        return f[-1]

        # @递推recurrence，原地修改
        m,n=len(obstacleGrid),len(obstacleGrid[0])
        f=obstacleGrid[0] # update@obs: get ref
        f[0]^=1 # # update@obs: 他1你就0; 他0你就1、不变
        for j in range(1,n):
            f[j]=0 if f[j] else f[j-1]
        for i in range(1,m):
            f[0]*=(1-obstacleGrid[i][0]) # update@obs: 他1你就0; 他0你就1、不变
            for j in range(1,n):
                f[j]=f[j]+f[j-1] if not obstacleGrid[i][j] else 0 # update@obs: 
        return f[-1]
```

[64](https://leetcode.cn/problems/minimum-path-sum/description/)@网格dp，递推，空间优化
```python
class Solution:
    def minPathSum(self, grid: List[List[int]]) -> int:
        # [[1,2,3],
        # [1,2,3]]

        # len(g)=2=i
        # len(g[0])=3=j

        # @cache # Omn
        # def dfs(i:int, j:int)->int:
        #     if i<0 or j<0:
        #         return inf
        #     if i==0 and j==0:
        #         return grid[i][j]
        #     return min(dfs(i-1,j),dfs(i,j-1)) + grid[i][j]
        
        # return dfs(len(grid)-1,len(grid[0])-1)
        
        # m,n = len(grid),len(grid[0])
        # f=[[inf]*(n+1) for _ in range(m+1)]
        # f[1][0]=0
        # for i,row in enumerate(grid):
        #     for j,x in enumerate(row):
        #         f[i+1][j+1]=min(f[i+1][j],f[i][j+1])+x
        # return f[m][n]

        m,n=len(grid),len(grid[0])
        f=grid[0]
        print(f)
        for j in range(1,n):
            f[j]+=f[j-1]
        print(f)
        for i in range(1,m):
            f[0]+=grid[i][0]
            for j in range(1,n):
                f[j] = min(f[j-1],f[j])+grid[i][j]
            print(f)
        return f[-1]
```

[152](https://leetcode.cn/problems/maximum-product-subarray/)乘积最大子数组@dp
```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        ans=-inf # ans may negative
        
        # f_max=max(f_max[i-1],f_min[i-1],1)*x
        # f_min=min(f_min[i-1],f_max[i-1],1)*x
        f_max=f_min=1
        for x in nums:
            f_max,f_min=max(f_max*x,f_min*x,1*x),min(f_max*x,f_min*x,1*x)
            ans=max(ans,f_max)
        
        return ans
```

[2140](https://leetcode.cn/problems/solving-questions-with-brainpower/solutions/1213919/dao-xu-dp-by-endlesscheng-2qkc/)@dp递推、递归@刷表法@never
```python
class Solution:
    def mostPoints(self, questions: List[List[int]]) -> int:
        n=len(questions)

        # @cache
        # def dfs(i)->int:
        #     if i>=n:
        #         return 0
        #     return max(dfs(i+1), dfs(i+1+questions[i][1])+questions[i][0])

        # return dfs(0)

        f=[0]*(n+100007)
        for i in range(n-1,-1,-1):
            # @choice: 或者在这里强设置上界
            # j=min(n,i+1+questions[i][1])
            f[i]=max(f[i+1],f[i+1+questions[i][1]]+questions[i][0])
        return f[0]

        # @刷表法@never
        n = len(questions)
        f = [0] * (n + 1)
        for i, (point, brainpower) in enumerate(questions):
            f[i + 1] = max(f[i + 1], f[i])
            j = min(i + brainpower + 1, n)
            f[j] = max(f[j], f[i] + point)
        return f[n]


```

[918环形子数组最大和](https://leetcode.cn/problems/maximum-sum-circular-subarray/description/)@数学，常数，分类讨论，kadane-维护局部最优的dp
```python
class Solution:
    def maxSubarraySumCircular(self, nums: List[int]) -> int:
        # 分类讨论。第一种情况，子数组没有跨边界，此时的最大子数组元素求和记作maxS
        # 第二种情况，子数组跨边界，由于全集元素求和为常数、定值sum(nums),于是此时最大跨边界子数组元素求和=sum(nums)-minS,minS为非跨边界子数组元素求和最小
        # 综上，取其最值ans=max(maxS,sum(nums)-minS)

        # 考虑特殊情况sum(nums)可以是整个数组记作,因子数组非空，于是此状态非法
        # 此情况下特判，返回max_s，亦即最大子数组元素求和@反证法，注意是"可以是"并非"iff"
        # return maxS

        max_s = -inf 
        min_s = 0
        max_f = min_f = 0
        for x in nums:
            max_f=max(max_f,0) + x # 局部最优
            max_s=max(max_s,max_f) # 全局最大

            min_f=min(min_f,0)+x # 局部最优
            min_s=min(min_s,min_f) # 全局最小

        # min_s==sum(nums)说明全是非正的，说明最大的也是非正的
        return max_s if max_s<=0 else max(max_s,sum(nums)-min_s)

        # # iff 最小子数组元素求和==整个数组求和，跨边界最大子数组为空
        # if sum(nums)==min_s:
        #     return max_s

        # # 否则考虑整体求和-min_s
        # return max(max_s,sum(nums)-min_s)
```

[1191k次串联之后最大子数组](https://leetcode.cn/problems/k-concatenation-maximum-sum/)@dp,max(sum(arr),0)分析
```python
MOD=1000_000_007
class Solution:
    def kConcatenationMaxSum(self, arr: List[int], k: int) -> int:
        def kadane(arr)->int:
            f1=f0=0
            for i in range(len(arr)):
                f0=max(0,f0)+arr[i]
                f1=max(f1,f0)
            return f1
        
        if k==1:
            return kadane(arr)

        ans=kadane(arr+arr)
        # 只有当s=sum(arr)>0时，答案要s*k
        ans+=max(sum(arr),0)*(k-2)
        return ans%MOD
```

[1749]任意子数组和的绝对值(https://leetcode.cn/problems/maximum-absolute-sum-of-any-subarray/)@前缀和，accumulate(nums,initial=0)@dp,空间优化
```python
class Solution:
    def maxAbsoluteSum(self, nums: List[int]) -> int:
        # @dp展开写法
        # # 禁止f1=f2=[0]可变对象赋值，a=b=0不可变对象赋值倒是可以
        # f1,f2=[0],[0]
        # for i in range(len(nums)):
        #     f1.append(max(0,f1[-1])+nums[i])
        #     f2.append(min(0,f2[-1])+nums[i])
        # return max(max(f1),-min(f2))

        # @dp空间优化写法，或者说kadane
        # ans=up=down=0
        # for x in nums:
        #     # f[i] = max(f[i-1]+nums[i],nums[i])=max(f[i-1],0)+nums[i]
        #     up=max(0,up)+x # 重开 | 不重开，拼接 ~ 维护最大
        #     down=min(0,down)+x # 重开 | 不重开，拼接 ~ 维护最小
        #     ans=max(ans,up,-down)
        # return ans

        # 子数组元素求和===值域数组元素点差
        # 加个初始元素0
        s=list(accumulate(nums,initial=0)) # 手动添加初始值
        return max(s)-min(s)
```

[53最大子数组和@前缀和](https://leetcode.cn/problems/maximum-subarray/description/)@贪心、dp
```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        # 前缀和 @贪心@dp

        # # kadane算法 On经典子数组和
        # # 用局部最优来更新全局最优
        # def kadane(nums):
        #     # 全局/局部
        #     max_sum = curr = nums[0]
        #     for num in nums[1:]:
        #         # 局部最优，当前元素结尾的最大子数组和
        #         curr = max(num, curr + num)  # 要么接着加，要么从当前重新开始
        #         # 全局最优
        #         max_sum = max(max_sum, curr) 
        #     return max_sum

        # return kadane(nums)

        # # @dp f[i]表示以nums[i]结尾的最大子数组和
        # # 转移方程：(1)独立成组 (2)和前面数组拼起来
        # f=[0]*len(nums)
        # f[0]=nums[0]
        # for i in range(1,len(nums)):
        #     f[i]= max(f[i-1],0)+nums[i]
        # return max(f)
        
        # @dp空间优化
        ans = -inf
        f=0 # 可以初始化为0或者任何-c
        for x in nums:
            f=max(f,0)+x
            ans=max(ans,f)
        return ans

        # @买股票121
        # 子数组的元素求和===两个前缀和的差
        ans=-inf
        min_pre_sum = pre_sum = 0
        for x in nums:
            pre_sum+=x # 更新前缀和
            ans=max(ans,pre_sum-min_pre_sum) # 前缀和-最小前缀和（全局最大）
            min_pre_sum=min(min_pre_sum,pre_sum) # 维护最小前缀和（局部最小）

        return ans
```

[3186](https://leetcode.cn/problems/maximum-total-damage-with-spell-casting/description/)施咒的最大伤害@打家劫舍198，值域数组，
```python
class Solution:
    def maximumTotalDamage(self, power: List[int]) -> int:
        # @递归
        # cnt=Counter(power)
        # a=sorted(cnt.keys())

        # @cache
        # def dfs(i:int)->int:
        #     if i<0:
        #         return 0
            
        #     x=a[i]
        #     # 最小合法的a[j]>=a[i]-2
        #     # 不选时候dfs(i)=dfs(j-1)+ a[i]*cnt[a[i]]
        #     j=i 
        #     while j and a[j-1] >= x-2:
        #         j-=1

        #     return max(dfs(i-1),dfs(j-1) + x*cnt[x]) # 用key*freq表示值域
        
        # return dfs(len(a)-1)

        # @递推
        cnt=Counter(power)
        # 合法数值数组a, a[j-1]相对于a[j]直接是一个合法有效值
        # sorted()排序
        a=sorted(cnt.keys())
        
        # f[i+1] === dp(i)
        # 边界dp(-1) 所以哨兵地，shfit右移1
        f=[0]*(len(a)+1) 
        j=0

        for i,x in enumerate(a):
            # 出来时候最小合法的a[j] >= x-2
            
            while a[j]<x-2:
                j+=1

            # 对于i+1来说，选择的情况下下一个合法的是a[i+1]-2 -1
            # 所以a[i]-1
            f[i+1]=max(f[i],f[j] + x*cnt[x])
        
        return f[-1]
```

[740删除并获得点数](https://leetcode.cn/problems/delete-and-earn/description/)@打家劫舍198，值域数组
```python
class Solution:
    # 预处理值域数组，等价成打家劫舍198问题
    def rob198(self,nums: List[int])->int:
        f0=f1=0
        for x in nums:
            f0,f1 = f1,max(f1,f0+x)
        return f1

    def deleteAndEarn(self, nums: List[int]) -> int:
        # [2,3,4]
        # 你拿-1，那么-2不能拿
        # 你拿-2，那么-1和-3也许不能拿
        a=[0]*(max(nums)+1)
        for x in nums:
            a[x] += x
        return self.rob198(a)
```

[2320](https://leetcode.cn/problems/count-number-of-ways-to-place-houses/description/)@线性dp
```python
MOD=1_000_000_007
MX=10_001

f=[1,2]
# 横着dp，因为两边所以乘法原理
while len(f)<MX:
    f.append((f[-1] + f[-2])%MOD)
class Solution:
    def countHousePlacements(self, n: int) -> int:
        return f[n] ** 2 % MOD # 乘法原理
```

213打家劫舍II@打家劫舍，条件分支，dp
```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        def dp(nums)->int:
            f0=f1=0
            for i,x in enumerate(nums):
                f0,f1 = f1,max(f1,f0+x)
            return f1
        
        return max(nums[0]+dp(nums[2:-1]),dp(nums[1:]))
```

198打家劫舍@dp
```python
class Solution:
    def rob(self, nums: List[int]) -> int:

        # # dp() 选择完索引i之后最大价值
        # @cache
        # def dp(i)->int:
        #     if i==0:
        #         return nums[0]
        #     if i==1:
        #         return max(nums[1],nums[0])
        #     # either do i-1, or i-2 and i
        #     return max(dp(i-1),dp(i-2)+nums[i])
        # return dp(len(nums)-1)

        # # @递推，相对索引
        # f=[0]*2
        # f[0]=nums[0]
        # f[1]=max(nums[:2])
        # for _ in range(len(nums)-2):
        #     f.append(max(f[-1],f[-2]+nums[len(f)]))
        # return f[-1]

        # @递推，f=[0]*(len(nums)+2)
        f=[0]*len(nums)+[0]*2
        for i,x in enumerate(nums):
            f[i+2]=max(f[i+1],f[i]+x)
        return f[-1]
```

2266@70爬楼梯，相对索引（dp、滑动窗口），dp，迭代器只能使用一次，list(groupby()[1])=["2","2","2"],list(groupby()[0])='2'
```python
MOD=1_000_000_007
f=[1,1,1+1,1+2+1] # 不为7/9
g=[1,1,1+1,1+2+1] # 7/9

# # f[i]/g[i] 定义为爬楼dp，表示长为i的只有一种字符串的字符所对应的文字信息种类
# for i in range(3+1, 10**5):
#     f.append((f[i-1] + f[i - 2] + f[i - 3]) % MOD)
#     g.append((g[i-4] + g[i - 1] + g[i - 2] + g[i - 3]) % MOD)

# 优化写法
# f相对索引，滑动窗口式状态转移，只关心前三个状态
# g相对索引，滑动窗口式状态转移，只关心前四个状态 
for _ in range(10**5-3):
    f.append((f[-1]+f[-2]+f[-3])%MOD) # case按一次 + case按二次 + case按三次
    g.append((g[-1]+g[-2]+g[-3]+g[-4])%MOD)

class Solution:
    def countTexts(self, pressedKeys: str) -> int:
        ans=1
        for ch,s in groupby(pressedKeys): # 迭代器只能用一次
            # 222332~ 2, group list化后是['2','2','2'] ~ 
            m=len(list(s)) # 长度m

            # 乘法原理，每次打字都是独立字串
            ans*= (g[m] if ch in "79" else f[m])%MOD # 要么79要么非79
        return ans%MOD
```

2466@70爬楼梯，dp
```python
class Solution:
    def countGoodStrings(self, low: int, high: int, zero: int, one: int) -> int:
        # nums=[zero,one]爬楼梯，排列
        # 在长为i-zero的字符串末尾家zero个0，方案数f[i-zero]
        # 在长为i-one的字符串末尾家one个1，方案数f[i-one]
        # 二者互斥，所以所有case=f[i-zero]+f[i-one]

        # 爬楼梯相当于zero,one=1,2
        # dp[i]=dp[i-1]+dp[i-2]

        # f[0]=1表示空串的方案数是1
        MOD=1_000_000_007
        @cache
        def dfs(i:int)->int:
            if i<0:
                return 0
            if i==0:
                return 1
            return (dfs(i-zero)+dfs(i-one))%MOD

        return sum(dfs(i) for i in range(low,high+1)) %MOD
        
```

377@排列（Permutation），类爬楼梯（每步决策消耗val），70爬楼梯
```python
class Solution:
    def combinationSum4(self, nums: List[int], target: int) -> int:
        # # dfs爬i个台阶的方案数
        # # 最后一步爬了x
        # # i-x台阶方案数
        # @cache
        # def dfs(i:int)->int:
        #     if i==0:
        #         return 1
        #     # 为什么最后一步爬x（x<=i）,然后枚举i是完全的？
        #     # 状态个数target, 状态计算时间n
        #     # 所以O(target*n)
        #     return sum(dfs(i-x) for x in nums if x<=i)
        # return dfs(target)

        # @down-to-up
        f=[1]+[0]*target # f[0]方案为1
        for i in range(1,target+1):
            f[i] = sum(f[i-x] for x in nums if i>=x) # 为什么要i>=x?
        return f[target]    
```

[3362III](https://leetcode.cn/problems/zero-array-transformation-iii/?envType=daily-question&envId=2025-05-22)@反悔贪心，最大堆（完全二叉、父节点>=子节点，取负模拟最大），O(qlogq)
```python
class Solution:
    def maxRemoval(self, nums: List[int], queries: List[List[int]]) -> int:
        # 最大堆维护左端点未选区间的右端点
        # 反悔贪心，用heap维护
        queries.sort(key=lambda q:q[0]) # 左端点ascending排序
        h=[]
        diff=[0]*(len(nums)+1)
        sum_d=j=0
        for i,x in enumerate(nums):
            sum_d+=diff[i]
            # 维护左端点<=i的区间
            while j<len(queries) and queries[j][0]<=i:
                heappush(h,-queries[j][1]) # 相反表示最大堆
                j+=1
            # 选择右端点最大区间
            while sum_d<x and h and -h[0]>=i:
                sum_d+=1
                diff[-heappop(h)+1]-=1
            if sum_d<x:
                return -1
        return len(h)
```

[3356II](https://leetcode.cn/problems/zero-array-transformation-ii/description/?envType=daily-question&envId=2025-05-21)@差分，二分 @lazy线段树 @双指针
```python
class Solution:
    def minZeroArray(self, nums: List[int], queries: List[List[int]]) -> int:
        # RTE
        # if set(nums)=={0}:
        #     return 0
        # k=0
        # diff=[0]*(len(nums)+1)
        # for (l,r,val) in queries: # Oq
        #     k+=1
        #     diff[l]+=val
        #     diff[r+1]-=val
        #     for x,sum_d in zip(nums,accumulate(diff)): # On
        #         # 至少有一个捡不完，那么就推出
        #         if x > sum_d:
        #             break
        #     else:
        #         # no exit then
        #         return k
        # return -1
        
        # @差分，二分
        # 开区间写法
        # 二分优化到 O((n+q)logq)
        def check(k:int)->bool: # O(q+n)
            diff=[0]*(len(nums)+1)
            for l,r,val in queries[:k]:
                diff[l],diff[r+1] = diff[l]+val, diff[r+1]-val
            
            for x,sum_d in zip(nums,accumulate(diff)):
                if x>sum_d:
                    return False
            return True
        
        # k越大越满足要求，于是单调，于是可以二分
        q=len(queries)
        left,right = -1,q+1 # 一眼开区间二分 
        while left+1<right: # log q，保证区间至少还有一个候选
            mid=(left+right)//2
            if check(mid):
                right=mid
            else:
                left=mid
        return right if right<=q else -1

        # @lazy线段树 
        # 线段树 O(n+qlogn)
        n=len(nums)
        m=2<<n.bit_length()
        mx=[0]*m
        todo=[0]*m

        def do(o:int,v:int)->None:
            mx[o]-=v
            todo[o]+=v

        def spread(o:int)->None:
            if todo[o] != 0:
                do(o*2,todo[o])
                do(o*2+1,todo[o])
                todo[o]=0
        
        def maintain(o:int)->None:
            mx[o]=max(mx[o*2],mx[o*2+1])
        
        def build(o:int,l:int,r:int)->None:
            if l==r:
                mx[o]=nums[l]
                return
            m=(l+r)//2
            build(o*2,l,m)
            build(o*2+1,m+1,r)
            maintain(o)

        def update(o:int,l:int,r:int,ql:int,qr:int,v:int)->None:
            if l>=ql and r <= qr:
                do(o,v)
                return
            spread(o)
            m=(r+l)//2
            if ql<=m:
                update(o*2,l,m,ql,qr,v)
            if m<qr:
                update(o*2+1,m+1,r,ql,qr,v)
            maintain(o)

        build(1,0,n-1)
        if mx[1]<=0:
            return 0

        for i,(ql,qr,v) in enumerate(queries):
            update(1,0,n-1,ql,qr,v)
            if mx[1]<=0:
                return i+1
        return -1

        # @双指针
        # 差分，双指针
        # o(n+q)
        diff=[0]*(len(nums)+1)
        sum_d=k=0
        # 外层On，内层Oq。
        # i，k负责nums，queries的双指针
        # 外层遍历nums是On，但是内层一共只走一次queries，于是O（n+q）
        
        # 引入摊销分析（Amortized）视角。
        # 相比单次更关心一系列耗时不高的情况，在本情况即为此。
        # 其他例子还有滑动窗口，双指针，并查集路径压缩，栈模拟
        for i,(x,d) in enumerate(zip(nums,diff)):
            sum_d+=d
            while k< len(queries) and x >sum_d: # 需要添加询问把x减小
                l,r,val=queries[k]
                diff[l]+=val
                diff[r+1]-=val
                if l<= i <=r: # x 在更新范围中
                    sum_d+=val
                k+=1
            if sum_d<x: # 无法更新
                return -1
        return k
```

[3355](https://leetcode.cn/problems/zero-array-transformation-i/description/?envType=daily-question&envId=2025-05-20)@diff,前缀和accumulate()
```python
class Solution:
    def isZeroArray(self, nums: List[int], queries: List[List[int]]) -> bool:
        # l..r 之间自由减一，最终nums是否都<=0 aka not >0
        # r+1位置需要操作，因此len()+1防止越界
        diff=[0]*(len(nums)+1)

        # 懒惰记录l..r进行减一
        # 例如
        # 1.对[0,0,0,0,0]的[1,4]加一，记作f([0,0,0,0,0,0],plus_one)=[0,+1,0,0,0,-1]
        # 2.还原accumulate([0,1,0,0,0,-1])=[0,1,1,1,1,1-1=0]=[0,1,1,1,1,0]
        # 得到1之差分数组[0,1,0,0,0,-1], 以及还原数组[0,1,1,1,1,0]
        # 于是得到差分数组操作f(l,+1,r+1,-1，diff[len(nums)+1])
        for l,r in queries:
            diff[l]+=1
            diff[r+1]-=1
        
        # accumulate() 前缀和 
        # 例如accumulate([1,2,3,4]) = [1,1+2=3,1+2+3=6,1+2+3+4=10] = [1,3,6,10]
        # accumulate()还原每个位置操作次数
        for x,sum_d in zip(nums,accumulate(diff)): 
            # sum_d是最多被减次数，大于说明不合法
            if x>sum_d:
                return False
        return True
```

[daily2901](https://leetcode.cn/problems/longest-unequal-adjacent-groups-subsequence-ii/description/?envType=daily-question&envId=2025-05-16)@后缀dp，from_路径数组，hamming距离
```python
class Solution:
    def getWordsInLongestSubsequence(self, words: List[str], groups: List[int]) -> List[str]:
        # hamming dis==1 and same len
        def check(s: str,t: str)->bool:
            a = sum(x!=y for x,y in zip(s,t))
            sn,tn=len(s),len(t)
            return sn==tn and a==1

        n=len(words)
        f=[0]*n ##
        from_=[0]*n 
        max_i=n-1

        # how to get max_i
        # what is f[]? 子序列的第一个字串是words[i]前提下，从后缀i到n-1中能选出的lss的长度
        for i in range(n-1,-1,-1): # -1 ~ before first elem and last elem
            # const i
            for j in range(i+1,n): # [i~,j]
                # f~> and goups diff and check true
                # then update i with j
                # from_ index i j
                if f[j]>f[i] and groups[j] != groups[i] and check(words[i],words[j]):
                    f[i]=f[j]
                    from_[i]=j
            # f[i]+=1
            f[i]+=1
            # update max f[i] idx
            if f[i]>f[max_i]:
                max_i = i

        # get max_i and traverse words into ans      
        i = max_i
        ans = ['']*f[i]
        for k in range(f[i]):
            ans[k] = words[i]
            i = from_[i]
        return ans
'''
n, words, groups
hamming distance: 等长字串的最小替换字串数量（描述性）。形式化地，等长字串或向量上相同位置但相应符号不同的数目之和，称作汉明距离。

'''
```
## hard

[3068](https://leetcode.cn/problems/find-the-maximum-sum-of-node-values/description/?envType=daily-question&envId=2025-05-23)@树形dp@结论，状态机dp@贪心
```python
class Solution:
    def maximumValueSum(self, nums: List[int], k: int, edges: List[List[int]]) -> int:
        # # @树形dp
        # g=[[] for _ in nums]
        # for x,y in edges:
        #     g[x].append(y)
        #     g[y].append(x)
        
        # def dfs(x:int,fa:int)->Tuple[int,int]:
        #     f0,f1=0,-inf # f[x][0],f[x][1]
        #     for y in g[x]:
        #         if y!= fa:
        #             r0,r1=dfs(y,x)
        #             f0,f1=max(f0+r0,f1+r1),max(f1+r0,f0+r1)
        #     return max(f0+nums[x],f1+(nums[x]^k)),max(f1+nums[x],f0+(nums[x]^k))
        # return dfs(0,-1)[0]

        # # @结论,状态机dp
        # f0,f1 = 0,-inf
        # for x in nums:
        #     f0,f1 = max(f0+x,f1+(x^k)),max(f1+x,f0+(x^k))
        # return f0

        # @贪心
        # 无向树，说明连通无向图
        # 原问题等价转换为“树上任意两点进行^k操作，使其所有节点数值求和最大”
        # 这样一来用贪心做是不用edges的
        res=sum(nums)
        diff=[(x^k)-x for x in nums]
        diff.sort() # Onlogn
        i=len(diff)-1
        # Q：为什么两两枚举是遍历完全的？
        # 等价转换之后相当于每个节点只能反转一次（XOR的反自反性质
        # 前置条件是排序，例如排序完的[-3,-2,-1,4,5,6]
        # [5,6]就不说了，[-1,4]受益于排序是可以要的
        # 于是贪心配对完全遍历
        while i>0 and diff[i]+diff[i-1]>=0:
            res+=diff[i]+diff[i-1]
            i-=2
        return res
```

[1931](https://leetcode.cn/problems/painting-a-grid-with-three-different-colors/description/?envType=daily-question&envId=2025-05-18)@递归，递推，邻接表，dfs(i,j)表示i列的方案对象j（j:=0..len(nv)-1），状态压缩，过滤，dp，for-else
```python
class Solution:
    def colorTheGrid(self, m: int, n: int) -> int:
        # 有效列方案valid
        pow3 = [3 ** i for i in range(m)] # 5*6 [1,3,9,27,81]
        valid = []
        for color in range(3 ** m):
            for i in range(1, m):
                if color // pow3[i] % 3 == color // pow3[i - 1] % 3:  # 相邻颜色相同
                    break
            else:  # 没有中途 break，合法
                valid.append(color)

        # 邻接表nxt，状态转移表nxt
        # nxt[j] = [...] 表示i（例如012）可以到j（201，120，...）
        nv = len(valid)
        nxt = [[] for _ in range(nv)]
        for i, color1 in enumerate(valid):
            for j, color2 in enumerate(valid):
                for p3 in pow3:
                    if color1 // p3 % 3 == color2 // p3 % 3:  # 相邻颜色相同
                        break
                else:  # 没有中途 break，合法
                    nxt[j].append(i) # 当前列是i可以从j转移过来（谁可以转过来）
        print(nxt)

        MOD = 1_000_000_007
        @cache  # 缓存装饰器，避免重复计算 dfs（一行代码实现记忆化）
        def dfs(i: int, j: int) -> int:
            if i == 0:
                return 1  # 找到了一个合法涂色方案
            return sum(dfs(i - 1, k) for k in nxt[j]) % MOD
        
        # 方案对象j从0~nv
        # 对于方案对象j，nxt[j]中所有的方案对象需要被上层列数遍历 
        # 特别的，当h==0时，dfs(i=0,j=x)代表对于m*i的matrix，;右边第i+1=1列填的是x的方案对象时的涂色方案数，亦即1
        return sum(dfs(n - 1, j) for j in range(nv)) % MOD

# dfs(i,j) m*i网格，i+1填的是valid[j]情况下的涂色方案数量
# 枚举valid[j]
# @邻接表nxt,
# @枚举列数col n和方案数k（in nxt）
# @dfs(n,j)代表枚举n+1列填充方案j(3)时候的
# 转移方程sum

# 1:1翻译成递推
class Solution:
    def colorTheGrid(self, m: int, n: int) -> int:
        pow3 = [3 ** i for i in range(m)]
        valid = []
        for color in range(3 ** m):
            for i in range(1, m):
                if color // pow3[i] % 3 == color // pow3[i - 1] % 3:  # 相邻颜色相同
                    break
            else:  # 没有中途 break，合法
                valid.append(color)

        nv = len(valid)
        nxt = [[] for _ in range(nv)]
        for i, color1 in enumerate(valid):
            for j, color2 in enumerate(valid):
                for p3 in pow3:
                    if color1 // p3 % 3 == color2 // p3 % 3:  # 相邻颜色相同
                        break
                else:  # 没有中途 break，合法
                    nxt[i].append(j)

        MOD = 1_000_000_007
        f = [[0] * nv for _ in range(n)]
        f[0] = [1] * nv  # dfs 的递归边界就是 DP 数组的初始值
        for i in range(1, n):
            for j in range(nv):
                f[i][j] = sum(f[i - 1][k] for k in nxt[j]) % MOD
        return sum(f[-1]) % MOD  # 递归入口就是答案
```

[daily-3337](https://leetcode.cn/problems/total-characters-in-string-after-transformations-ii/description/?envType=daily-question&envId=2025-05-14)@dp, 矩阵乘法, 快速幂

```python
# 矩阵乘法
# List[List[int]]
def mul(m: List[List[int]], b:List[List[int]]) -> List[List[int]]:
    for row in m:
        for col in zip(*b): # 转置列矩阵b为行矩阵 
            for x,y in zip(row,col):
                ans+=x*y
    return ans
    # 或者写成生成表达式
    sum(x*y for y in col)
    sum(x*y for y in col) for col in zip(*b)  
    [sum(x*y for y in col) for col in zip(*b)]for row in m
    [[sum(x*y for y in col) for col in zip(*b)]for row in m]
    return [[sum(x*y for y in col) for col in zip(*b)] for row in m]
# np.nparray
f0 = np.ones((SIZE,), dtype=object)
m = np.zeros((SIZE, SIZE), dtype=object)
def mul_np(m: np.ndarray, b: np.ndarray) -> np.ndarray: # n-dimensional array
    return m@b
```
```python
# 快速幂
# List快速幂
def pow_mul(a: List[List[int]], n: int, f0: List[List[int]]) -> List[List[int]]:
    res = f0
    while n:
        if n & 1: # 如果n&1为真，则做一次乘法
            res = mul(a, res)
        a = mul(a, a) # a平方
        n >>= 1 # n减半
    return res
# np.ndarray快速幂
# a^n @ f0
def pow_mul(a: np.ndarray, n: int, f0: np.ndarray) -> np.ndarray:
    res = f0
    while n:
        if n & 1:
            res = a @ res % MOD
        a = a @ a % MOD
        n >>= 1
    return res
```
```python
# 题解：dp+矩阵+快速幂
class Solution:
# 200   ~ O(n3)
# 10^3  ~ O(n2)
# 10^5  ~ O(n), O(nlogn)
# 10*9  ~  
# 10*18 ~ O(1), O(logn)
MOD = 1_000_000_007
    def lengthAfterTransformations(self, s: str, t: int, nums: List[int]) -> int:
        SIZE = 26 # 字母表
        f0 = [[1] for _ in range(SIZE)] # 初始化列向量f[0]: (26,1)
        m = [[0] * SIZE for _ in range(SIZE)] # 归零操作向量m: (26,26)
        for i, c in enumerate(nums): # 初始化操作向量m, 使其符合转移方程
            for j in range(i + 1, i + c + 1):
                m[i][j % SIZE] = 1
        # O(26^3*log t + n)
        mt = pow_mul(m, t, f0) # 快速幂计算f[t]:(26,1)

        ans = 0 # 初始化ans
        for ch, cnt in Counter(s).items(): # cnt是字母出现频率，ord()是unicode编码
            ans += mt[ord(ch) - ord('a')][0] * cnt
        return ans % MOD
```