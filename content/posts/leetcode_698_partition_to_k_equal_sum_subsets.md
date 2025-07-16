---
date: '2025-07-16T16:37:44+05:30'
author: abdul
math: true
title: 'Leetcode 698: Partition into k equal sum subsets: The art of recursion: mastering double recursion in a single function'
---

We need to divide the array `nums` into `k` subsets such that the sum of each subset is same.

$$
k \times sum\ of\ each\ subset = total\ sum\\
sum\ of\ each\ subset = total\ sum\ \div k\\
target = total\ sum\ \div k
$$

Now our task is to **find all the `k` subsets in nums whose sum is `target`**.

My idea is to structure this as multi-level recursion. We first try to find the $$k^{th}$$ subset, then $$(k-1)^{th}$$, then $$(k-2)^{th} ...$$ until there is only one subset left. The last subset will naturally sum to `target`. You can only go to $$(k-1)^{th}$$ level when you are able to successfully find $$k^{th}$$ level subset,

For example,

```
..
..
find the 5th subset
find the 4th subset
find the 3rd subset
find the 2nd subset
return true
```

Seeing the recursive structure.

```
nums = [4,3,2,3,5,2,1]   k=4
target = 20 / 4 = 5

suppose you find the 4th subset as: {3,2} from [4,3,2,3,5,2,1]
Now your task reduces to finding 3 subsets  in [4,   ,3,4,2,1]
that sum to target=5

recursive call: 
nums=[4,   ,3,4,2,1]   k=3
```

When finding the kth subset. You can simply use pick/not pick backtracking approach. It is important to understant the distinction between same level recursion (at any given k) which operates by deciding weather to pick/drop nums@i. 

And the **k levels** recursion which succeeds/fails by checking if we can form the $$k^{th}$$ subset.

```python3 []
class Solution:
    def __init__(self):
        self.nums, self.target, self.used = None, None, None

    def canPartitionKSubsets(self, nums: List[int], k: int) -> bool:
        self.nums = nums
        total = sum(self.nums)
        self.target = total // k
        self.used = [False] * len(nums)

        if total % k != 0: return False

        self.nums.sort(reverse=True)
        return self.bt_search(0, k, 0)

    def bt_search(self, i: int, k: int, cur_sum: int) -> bool:
        if k == 1: # last subset is sure to sum upto target
            return True
        if i >= len(self.nums) or cur_sum > self.target:
            return False
        if cur_sum == self.target: # current subset found, 
            return self.bt_search(0, k-1, 0) # explore the next level of k
        
        # try picking nums@i in current subset
        if not self.used[i] and cur_sum + self.nums[i] <= self.target:
            self.used[i] = True
            # explore in same level
            if self.bt_search(i+1, k, cur_sum + self.nums[i]):
                return True
            self.used[i] = False

            # on failure, skip duplicates of nums@i
            while i+1 < len(self.nums) and self.nums[i] == self.nums[i+1]:
                i += 1
        
        # do not pick nums@i -> explore in same level
        return self.bt_search(i+1, k, cur_sum)
```
