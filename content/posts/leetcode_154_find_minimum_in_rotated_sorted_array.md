---
date: '2025-07-16T15:24:12+05:30'
draft: false
title: 'Leetcode 154: Find minimum in rotated sorted array'
math: true
---

Below is my solution for the [leetcode 154: Find minimum in rotated
sorted array problem](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array-ii/description/)

# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->

Fact is, that you **cannot** solve this question in **O(log n)** time. The reason is because of duplicates.

Consider a situation like:

$$
[2,2,2,2,1,2,2]
$$

where mid is at 3 and the minium number here is clearly $$1$$. But our binary search algorithm will not be able to figure out in which direction it should go, since starting, ending and middle values are all same. In this case the best we can do is increment mid which makes the worst running time: **O(n)**

However, it is possible to solve this problem in **O(n/2)** as explain below.

# Approach
<!-- Describe your approach to solving the problem. -->

We will make use of the fact that a sorted array follows the (min) heap  property i.e. in a sorted array, at all parent nodes are smaller than their children.

And if this sorted array is rotated, then the place at which the first violation occurs is the subtree where the answer will be found.

# Complexity
- Time complexity:  $$O(n/2)$$
 <!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity: $$O(1)$$
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

# Code
```javascript []
var findMin = function(a) {
    /**
       Approach: 

       You cannot solve this problem in O(log n) since it contains duplicate
       values. But you can actually solve this problem in O(n/2) by using the
       fact that a sorted array follows heap property. And if a sorted array is
       rotated then the heap property no longer holds. to find the minium
       element traverse the array, and find the first subtree where the heap
       property does not hold. the minium of the 2 values where the heap
       property does not satisfy is also minium in the array.

       Time: O(n/2)
       Space: O(1)
    */
    let A = a.length;
    let i = 0;
    
    while ( i <= Math.floor(A/2)-1 ) {
        let root = i;
        let lc = 2*i + 1;
        let rc = 2*i + 2;
        let smallest = root;

        if ( lc < A && a[lc] < a[smallest] ) smallest = lc;
        if ( rc < A && a[rc] < a[smallest] ) smallest = rc;

        if ( smallest != root )
            /* violation found. the value at smallest is minium */
            return a[smallest];
        i++;
    }
    
    /* no violation found. Either the array is sorted ar there
       it is all duplicates, in either case, return the first
       element.
    */
    return a[0];
};
```
