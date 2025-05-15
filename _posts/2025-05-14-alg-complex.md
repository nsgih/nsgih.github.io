---
layout: post
title: alg complex
date: 2025-05-14 23:43 +0800
tag: complex
---
## 脚手架
### python
- list comprehension
> “Comprehension” means:
> Grabbing and shaping data from something else
> using a compact expression.
>
> Grasping values from an iterable (for x in ...)
> 
> Filtering them optionally (if ...)
> 
> Transforming them (expression)
> 
> Composing a new collection (list, set, dict)
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

## hard
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