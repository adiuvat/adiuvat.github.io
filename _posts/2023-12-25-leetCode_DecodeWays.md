---
layout: post
title:  "Leetcode practice 20231225- Decode ways & Zigzag"
categories: LeetCode
tags:  LeetCode
author: Marcus
catalog: yes
---

* content
{:toc}
# Decode ways

## [Decode ways](https://leetcode.com/problems/decode-ways/description) - Medium

**Thoughts: **

For String s with length n, to calculate the number of decode ways DecodeNum(s), we can first calculate the number of decode of two substring:

s[:n-1], which leaves out one digit

s[:n-2], which leaves out two digit

If both leaved out one digit and two digit forms a valid number, then: 

DecodeNum(s) = DecodeNum(s[:n-1]) + DecodeNum(s[:n-2])

**Code**

```Python
class Solution:
    def numDecodings(self, s: str) -> int:
        if s[0] == '0':
            return 0
        n = len(s)
        dp = [0]*(n+1)
        dp[0] = dp[1] = 1
        for i in range(2, n+1):
            twodigit = int(s[i-2:i])
            if s[i-1] != '0':
                dp[i] += dp[i-1]
            if twodigit <= 26 and twodigit >= 10:
                dp[i] += dp[i-2]
        return dp[n]
```



## [Decode Ways II](https://leetcode.com/problems/decode-ways-ii/) - Hard

**Thoughts: **

For String s with length n, to calculate the number of decode ways DecodeNum(s), we can first calculate the number of decode of two substring:

s[:n-1], which leaves out one digit. Calculate how many ways to decode that one digit

s[:n-2], which leaves out two digit. Calculate how many ways to decode that two digit

DecodeNum(s) = oneDigitWays * DecodeNum(s[:n-1]) +twoDigitWays * DecodeNum(s[:n-2])

**Code:**

```Python
class Solution:
    def numDecodings(self, s: str) -> int:
        def calOneDigit(char):
            if char == '0':
                return 0
            elif char == '*':
                return 9
            else:
                return 1

        def calTwoDigit(s):
            if s[0] == '*' and s[1] == '*':
                return 15
            elif s[0] == '*':
                if s[1] > '6':
                    return 1
                else:
                    return 2
            elif s[1] == '*':
                if s[0] < '2' and s[0] > '0':
                    return 9
                elif s[0] == '2':
                    return 6
                else:
                    return 0
            else:
                num = int(s)
                if num <= 26 and num >= 10:
                    return 1
                else:
                    return 0


        MOD = 10 ** 9 + 7    
        if s[0] == '0':
            return 0
        n = len(s)
        dp = [0]*(n+1)
        dp[0] = 1
        dp[1] = 9 if s[0] == '*' else 1
        for i in range(2, n+1):
            dp[i] += dp[i-1] * calOneDigit(s[i-1])
            dp[i] += dp[i-2] * calTwoDigit(s[i-2:i])
            dp[i] %= MOD
        return dp[n]

```

