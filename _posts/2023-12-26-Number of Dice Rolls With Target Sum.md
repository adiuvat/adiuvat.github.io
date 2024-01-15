---
layout: post
title:  "Leetcode practice 20231226- Number of Dice Rolls With Target Sum"
categories: LeetCode
tags:  LeetCode
author: Marcus
catalog: yes
---

* content
{:toc}

# [Number of Dice Rolls With Target Sum](https://leetcode.com/problems/number-of-dice-rolls-with-target-sum/description/)

**Thoughts:**

`dp[i][j]` stands for having `i` chances left to roll a sum of `j`
$$
dp[i][j] = \sum_{l = 1}^{k} dp[i-1][j-l]
$$

```python
class Solution:
    def numRollsToTarget(self, n: int, k: int, target: int) -> int:
        MOD = 10 ** 9 + 7
        @cache
        def dfs(n_left, target_left):
            # find one
            if n_left == 0 and target_left == 0:
                return 1
            # impossible, skip
            if n_left > target_left or n_left * k < target_left:
                return 0
            ans = 0
            for i in range(1, min(k, target_left)+1):
                ans = (ans + dfs(n_left-1, target_left-i)) % MOD
            return ans
        
        return dfs(n, target)
```

&nbsp;

&nbsp;



# [Minimum Time to Make Rope Colorful](https://leetcode.com/problems/minimum-time-to-make-rope-colorful/)

**思路：**
1. 找出所有重复区间，用双指针可以很容易找到
2. 计算每个区间的花费， 因为要花费最低，因此必然保留花费最大的那个，去掉剩下的。即sum-max

优化：
可以将两部合二为一，即在寻找区间的同时，维护这个区间内的sum和max，在区间结束后直接计算花费。

```python
class Solution:
    def minCost(self, colors: str, neededTime: List[int]) -> int:
        N = len(colors)
        if N == 1:
            return 0
        left = right = ans = 0 
        while left < N:
            # initialize 
            curMax = neededTime[left]
            curSum = neededTime[left]
            right = left + 1
            # find the interval with the same color
            while right < N and colors[left] == colors[right]:
                curMax = max(curMax, neededTime[right])
                curSum += neededTime[right]
                right += 1
            # remove all balloons in the interval except the one takes longest time to remove
            ans += (curSum-curMax)
            # reset left pointer
            left = right
        return ans
            
```

