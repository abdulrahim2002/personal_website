---
date: '2025-07-16T16:20:16+05:30'
draft: false
author: abdul
math: true
title: 'Leetcode 769: Max chunks to make sorted solution'
---

When numbers from $$\in$$ `[0,n-1]` are sorted in an array of size n. Their sorted position is equal to their index.

![image.png](https://assets.leetcode.com/users/images/07a3b1d2-9478-486f-9f62-3a67e6dde1b3_1752513551.205261.png)

Subset of numbers in `array[i:j]` can form a partition, if all elements in `[i,j)` are available in `array[i:j]`.

For example `[2,0,1]` can form a partition, since they are at index `0, 1, 2` respectively. Sorted will involving swapping them at their correct position.

![image.png](https://assets.leetcode.com/users/images/77d9235f-d72a-416a-bd77-d8802d7567b0_1752513786.651974.png)


The basic idea behind this solution is that we try to identify such partitions, where all elements required to be sorted in `[i, j]` are available in current partition.

Here's the visualization of the algorithm, `i` points to the start of the group and `j` iterates through the group and checks if the current group needs to be expanded. `e` points to the end of the current group.

![image.png](https://assets.leetcode.com/users/images/d46538ff-fbec-46cf-942f-5dd8bc02e152_1752514625.6962414.png)

Since, array[j]=1, we found a number greater than current boundaries. We need to expand the boundary. Hence, new group end is `e` = array[j].

![image.png](https://assets.leetcode.com/users/images/df9fd405-1462-4991-bbe4-89170b0d75af_1752514705.3578908.png)

Increment j. We find that array[1] = 0 which is < current end. 
Increment j again, j=2, Hence, no we exhaust the current group and we move forward to finding the next partition. Increase, current group ending, and reinitialize i=e/

![image.png](https://assets.leetcode.com/users/images/fbfa16c8-a24a-4909-acb0-f2ec106eead6_1752514834.8952081.png)


![image.png](https://assets.leetcode.com/users/images/cc324778-fb0f-4c55-8d5d-60aeac398805_1752515101.2236679.png)

In the second group, we have `array[j]=2` and we are at index 2. Hence, we do not need to expand this group, since 2 is at right position.

Similarly we find the next 2 partitions.

![image.png](https://assets.leetcode.com/users/images/d8c32709-4514-46ff-aefe-62bca55f013d_1752515291.6334476.png)



```c []
int maxChunksToSorted(int* arr, int arrSize) 
{
    int cur_gp_end = 0, cnt_gps = 0, i = 0;
    while ( i < arrSize ) {
        int j = i;
        // try to terminate the current group
        while ( j <= cur_gp_end ) {
            cur_gp_end = fmax( cur_gp_end, arr[j] );
            j++;
        }
        i = ++cur_gp_end;
        cnt_gps++;
    }
    return cnt_gps;
}
```
