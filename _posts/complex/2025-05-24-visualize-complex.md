---
layout: post
title: 相
tag: complex
hidden: True
date: 2025-05-24 19:38 +0800
---

[![Static Badge](https://img.shields.io/badge/screen2gif-%E5%BC%80%E6%BA%90-55acee)](https://github.com/NickeManarin/ScreenToGif)

## alg

### dp@kadane

```python
# kadane算法 On经典子数组和
# 用局部最优来更新全局最优
def kadane(nums):
    # 全局/局部
    ans = local = nums[0]
    for x in nums[1:]:
        # 局部最优，当前元素结尾的最大子数组和
        local = max(x, local + x)  # 要么接着加，要么从当前重新开始
        # 全局最优
        ans = max(ans, local) 
    return ans

return kadane(nums)
```

![alt text](/assets/2025-05/kadane.gif)_kadane是dp的一种特殊情况_