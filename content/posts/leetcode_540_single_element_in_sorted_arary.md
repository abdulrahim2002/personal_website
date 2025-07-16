---
date: '2025-07-16T15:51:01+05:30'
math: true
author: abdul
title: 'Leetcode 540: Single element in sorted array'
---

## Explanation

```
Consider the number: 
[1(0), 1(1), 2(2), 3(3), 3(4), 4(5), 4(6), 8(7), 8(8)]
    , number(index)

Here, the single number is 2, let's call it SN

Note that:
- For each duplicate pair that appears to the left 
  of SN, the first occurence appears at even index 
  and second occurence appears at odd index
- For each duplicate pair that appears to the right 
  of SN, the first occurence appears at odd index 
  and second occurence appears at even index

Now to easily detect weather we are to the left or
right of SN, we can employ XOR with 1 operator.

Property of XOR with 1 operator:

XOR_1(x) = x ^ 1 = x + 1   , when x is even
XOR_1(x) = x ^ 1 = x - 1   , when x is odd

From this knowledge, it can be infered that for each index i:

number[ i ]  = number[ XOR_1(i) ]  ,when i is to 
                                    left of SN 
number[ i ] != number[ XOR_1(i) ]  ,when i is at SN or to 
                                    the right of SN

For example, at i=1, i is to left of SN

number[i] = number[1] = 1 == number[ XOR_1(1) ] 
= number[ 1-1 ] = number[0] = 1

at i=2, i is at Single Number (SN)

number[2] = 2 != number[XOR_1(2)] = number[3] = 3

at i=6, i is to right of SN
number[6] = 4 != number[XOR_1(6)] = number[7] = 8

You can verify this for any i

Now our job is to find the minimum valud of i such that
number[i] != number[XOR_1(i)]

This can be done using binary search.

It is worth mentioning that at i=8 in the above example
number[ XOR_1(8) ] = number[ 9 ] which is out of bonds

We avoid this situation by using ( i < j ) as loop 
condition. Which implies that i will never reach the 
end to make mid = 9

The binary search is designed such that j always takes
valid values, where valid means nums[j] != nums[j^1]

When i == j, we are at the leftmost valid index, which
is exactly what we require
```

## Code

```typescript []
function singleNonDuplicate(nums: number[]): number {
    const N = nums.length;

    let i = 0, j = N-1;

    while ( i < j ) {
        const mid = i + Math.floor( (j-i) / 2 );

        if ( nums[mid] != nums[mid^1] )
            j = mid;
        else
            i = mid + 1;
    }

    return nums[j];
};
```
