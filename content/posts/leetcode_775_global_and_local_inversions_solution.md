---
date: '2025-07-16T16:40:08+05:30'
author: abdul
math: true
title: 'Leetcode 775: Global and local inversions solution'
---

When `nums[i] > nums[j]`, it is global inversion. Local inversion is special case of global inversion with `j=i+1`

Consider the array: `[0,1,2,3,4,5]`

![image.png](https://assets.leetcode.com/users/images/2f3eb772-cc4d-4006-adec-d2937264734b_1752435521.47083.png)


If i swap a random element by 2 positions (either to left or right). I will always create 2 inversions.

![image.png](https://assets.leetcode.com/users/images/36424827-47b3-4fab-b464-6a02cbd9a953_1752435463.2740006.png)


![image.png](https://assets.leetcode.com/users/images/c9cefbb6-24e0-419e-8e4e-69178894068a_1752435700.7604558.png)


Likewise, if I swap a random element by 3 positions. I end up creating 3 inversions.

![image.png](https://assets.leetcode.com/users/images/a5633f7f-c730-4671-8262-daed0eba9b80_1752435799.3669355.png)


Similarly if I swap an element by 4 positions. I end up creating 4 inversions.

![image.png](https://assets.leetcode.com/users/images/a26554ff-17a6-44f2-b529-cffb2cae4de4_1752435935.4414523.png)


**Note that for each swap made above, we created a single local inversion.** This means that whever we swap 2 elements, we end up creating `1` local inversion and `k` global inversions. Where `k means element swapped k number of positions away`.


Now, we need `number of local inversions = number of global inversions`. This can only happen when local inversion is itself the global inversion.

Hence, for each swap we should only create 1 inversion, THEREFORE `K=1`

```c []
bool isIdealPermutation(int* nums, int numsSize) 
{
    for (int i=0; i < numsSize; i++) {
        if ( abs(nums[i]-i) > 1 ) 
            return false; 
    }
    return true;
}
```
