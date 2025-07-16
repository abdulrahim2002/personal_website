---
date: '2025-07-16T15:48:46+05:30'
title: 'Leetcode_525_contiguous_array'
author: abdul
math: true
---

Consider the array: `[0,1,1,0,0,1,1,0,1,1] `

The idea is to turn the 0's into -1's

array: ` [-1,1,1,-1,-1,1,1,-1,1,1] `

Now, the task is to find a subarray with `sum of elements = 0`

To do this, we can build the prefix array with: $$ prefix\ [x] = \sum_{k=0}^x nums\ [k] $$

The sum of elements between subarray indices $$[i, j]$$ where $$j > i$$ is defined as:

$$
prefix\ [j]\ -\ prefix\ [i-1] = \sum_{k=0}^{j} nums\ [k] - \sum_{k=0}^{i-1} nums\ [k]\\
prefix\ [j]\ -\ prefix\ [i-1] = \sum_{k=i}^{j} nums\ [k]
$$

And the length of the subarray between indices $$[i,j]$$ is defined as: $$length(i,j) = j-i+1 = j-(i-1) $$

Now we are looking for `sub of subarray = 0`. Therefore:

$$ 
sum\ of\ subarray = prefix\ [j]\ -\ prefix\ [i-1] = 0\\
prefix\ [j]\ =\ prefix\ [i-1]
$$

Therefore, we iterate the prefix array. And at each `prefix[j]`, we search for a previously inserted `prefix[i-1]` such that `prefix[j]=prefix[i-1]`. We record the length of the current subarray as: `length = j-(i-1)`.

We must store the mapping: `( prefix[k], k )` in a map to achieve this.

So far so good. But what happens when $$i=0$$. In this case, sum of subarray between indices `[0, j]` is defined as: 

$$
prefiix\ [j] = \sum_{k=0}^j nums\ [k]
$$

And the length of the subarray `[0,j]` is: $$j+1 = j-(-1)$$

Therefore we keep a superficial `(prefix = 0, index = -1)` in the map.



---
```typescript []
function findMaxLength(nums: number[]): number {
    const map = new Map<number, number>( [
        [0, -1],
    ] );

    let maxLen = 0;

    for ( let j=0; j < nums.length; j++ ) {
        let cur = ( nums[j] == 1 ) ? 1 : -1;
        nums[j] = ( j==0 ) ? cur : cur + nums[j-1];

        // prefix sum occured previously at i. Length = j-i
        if ( map.has( nums[j] ) ) {
            let i = map.get( nums[j] );
            maxLen = Math.max( maxLen, j - i );
        }

        // put the current prefix sum into map
        if ( !map.has( nums[j] ) ) 
            map.set( nums[j], j );
    }

    return maxLen;
};
```
