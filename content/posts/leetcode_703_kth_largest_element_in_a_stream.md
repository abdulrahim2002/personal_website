---
date: '2025-07-16T16:22:08+05:30'
draft: false
author: abdul
math: true
title: 'Leetcode 703: kth largest element in a stream solution in python'
---

I keep track of the top k elements in sorted list. 
The kth largest element is the **smallest of the top k elements**.

e.g. top k(=6) elements: `[3,5,7,10,42,56]` in sorted order.
The kth largest element = 6th largest element = smallest element in above list.

![image.png](https://assets.leetcode.com/users/images/070564ea-afa3-4108-92e6-7fa76c2fb981_1752504996.0140414.png)


When I insert an element, I simply check if it can make it's place among k largest elements already present in the list. Which it can, if it can defeat the smallest element in our list.

For example, we cannot add `2` in the array above since it fails to defeat `3`. 

![image.png](https://assets.leetcode.com/users/images/117aa6b3-e417-46be-8b17-740577a4b741_1752505172.1847956.png)

However, A number like `15` can be inserted. In which case, we remove the smallest element i.e. `3`, to keep the list length=`k`

![image.png](https://assets.leetcode.com/users/images/ba81c74b-2080-472a-84e8-f4868490e25a_1752505275.6821146.png)


```python3 []
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.scores = SortedList()
        self.limit = k
        for num in nums:
            self.add(num)

    def add(self, val: int) -> int:
        # if the current element can make space in top k elements
        if len(self.scores) < self.limit or self.scores[0] < val:
            self.scores.add(val)

        # remove smallest
        if len(self.scores) > self.limit:
            self.scores.pop(0)
        
        # return smallest
        return self.scores[0]
```
