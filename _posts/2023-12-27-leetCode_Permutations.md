---
layout: post
title:  "Leetcode practice 20231227- Permutations & Next permutations "
categories: LeetCode
tags:  LeetCode dfs Dynamic-Programming
author: Marcus
catalog: yes
---

* content
{:toc}

# [46. Permutations](https://leetcode.com/problems/permutations/)

**Thoughts:**

Use dfs + backtracking to get all possible permutations.

use array `path` to store temporary result， and array `visited` to store usage of each element

```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        ans = []
        def dfs(path, visited):
            if len(path) == len(nums):
                ans.append(path[:])
                return
            for i in range(len(nums)):
                if not visited[i]:
                    path.append(nums[i])
                    visited[i] = True
                    dfs(path, visited)
                    visited[i] = False
                    path.pop()

        dfs([], [False]*len(nums))
        return ans
```

&nbsp;

&nbsp;



# [31. Next Permutation](https://leetcode.com/problems/next-permutation/)

https://www.geeksforgeeks.org/next-permutation/

> We want to find a larger number, but not too large - just the next larger one =>
> We want to avoid chaning the number in the left - it will increase the number too much =>
> Example: 4325413 -> we can only change the last two numbers and have 432531
> What if it was 432531 in the first place? 31 cannot be increased.
> Lets try 531 - still no
> 2531 - this can be incrased - the smallest number that can be used to incrase the 2 is 3. so for now we have 3521.
> Next we want to minimize 3521 - thats easier - just sort the numbers to the right of 3 - 3125. So the answer is 433125


```python
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        n = len(nums)
        i = n-2
        # find the breaking point before longest descending suffix
        while i >= 0:
            if nums[i] < nums[i+1]:
                break
            i -= 1
        # if the whole array is descending, reverse it
        if i < 0:
            nums.reverse()
        else:
            # find the smallest number that is greater than the breaking point number (to replace it)
            for j in range(n-1, i, -1):
                if nums[j] > nums[i]:
                    break
            # swap the two
            tmp = nums[i]
            nums[i] = nums[j]
            nums[j] = tmp
            # reverse the suffix in order to minimize it
            nums[i+1:] = nums[i+1:][::-1]

```

