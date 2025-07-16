---
date: '2025-07-16T15:54:08+05:30'
draft: false
math: true
author: abdul
title: 'Leetcode 560: Subarray sum equals k'
---

The task is to find a subarray with `sum of elements = k`

To do this, we can build the prefix array with: $$ prefix\ [x] = \sum_{k=0}^x nums\ [k] $$

The sum of elements between subarray indices $$[i, j]$$ where $$j > i$$ is defined as:

$$
prefix\ [j]\ -\ prefix\ [i-1] = \sum_{k=0}^{j} nums\ [k] - \sum_{k=0}^{i-1} nums\ [k]\\
prefix\ [j]\ -\ prefix\ [i-1] = \sum_{k=i}^{j} nums\ [k]
$$

Now we are looking for `sub of subarray = T(target)`. Therefore:

$$ 
sum\ of\ subarray = prefix\ [j]\ -\ prefix\ [i-1] = T\\
\ prefix\ [i-1] = prefix\ [j]\ - T
$$

Therefore, we iterate the prefix array. And at each `prefix[j]`, we search for a previously inserted `prefix[i-1]` such that `prefix[i-1] = prefix[j] - T`. We also need the count of such subarrays. At a particular `j` the number of subarrays where `sum=T` is the number of `i` that occured previously such that `prefix[i-1] = prefix[j] - T`. Index `j` will form a subarray with each of such previous `i`'s.

We must store the mapping: `( prefix[x], count )` in a map.

So far so good. But what happens when $$i=0$$. In this case, sum of subarray between indices `[0, j]` is defined as: 

$$
prefix\ [j] = \sum_{k=0}^j nums\ [k] = T
$$

At each index `j`, the code will try to look for `prefix[j] - T` in the map. When `prefix[j]=T` itself, then it would look for `T-T=0` into the map. Therefore we must keep `prefix= 0, count= 1` into the map to account for subarrays starting at index 0.

# Code
```typescript []
function subarraySum(nums: number[], k: number): number {
    const map = new Map<number, number>([ [0, 1] ]);

    let sumk = 0;

    for ( let j=0; j < nums.length; j++ ) {
        nums[j] = ( j==0 ) ? nums[j] : nums[j-1] + nums[j];
        sumk += map.get( nums[j] - k ) ?? 0;
        map.set( nums[j], ( map.get(nums[j]) ?? 0 ) + 1 );
    }
    
    return sumk;
};

```
