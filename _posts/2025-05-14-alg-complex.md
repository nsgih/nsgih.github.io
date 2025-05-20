---
layout: post
title: alg complex
date: 2025-05-14 23:43 +0800
tag: complex
---
## 脚手架

### [排序sort](https://visualgo.net/zh/sorting)

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
- list comprehension

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
[3355](https://leetcode.cn/problems/zero-array-transformation-i/description/?envType=daily-question&envId=2025-05-20)@diff,前缀和
```python
class Solution:
    def isZeroArray(self, nums: List[int], queries: List[List[int]]) -> bool:
        diff=[0]*(len(nums)+1)
        for l,r in queries:
            diff[l]+=1
            diff[r+1]-=1
        
        for x,sum_d in zip(nums,accumulate(diff)):
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