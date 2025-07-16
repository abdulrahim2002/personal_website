---
date: '2025-07-16T16:15:18+05:30'
draft: true
math: true
author: abdul
title: 'Leetcode 667: Beautiful arrangements 2 solution'
---

## Problem definition:

For the output array, you need to calculate the absolute difference of each adjacent items. The number of unique absolute differences needs to be exactly k.
For example in [1,3,2]:
abs(1-3) = 2
abs(3-2) = 1
Thus [2,1] with 2 unique values as in example 2.
credits: [@Frans Valli](/u/FransV)

## Approach:

Divide the array into 2 halves. The left half will contain numbers continously increasing by 1. The right half will contain numbers, such that the **differences** are continously decreasing by 1.

Move the max element to (n-k) index. The left half will have `1, 2, 3, ... n-k+1`. The right half will contain minimum and maximum numbers (from the elements that are left over)

Example:
For n=6 and k=1: Base case: max is at 6th position:
1,2,3,4,5,6
differences:
 1,1,1,1,1

For n=6 and k=2: Max is at 5th position
1,2,3,4,6,5
differences:
 1,1,1,2,1 -> 2 unique

For n=6 and k=3: Max is at 4th position
1,2,3,6,4,5
differences:
 1,1,3,2,1 -> 3 unique

For n=6 and k=4: Max is at 3rd position
1,2,6,3,5,4
differences:
 1,4,3,2,1   -> 4 unique

For n=6 and k=5: Max is at 2nd position
1,6,2,5,3,4
differences:
 5,4,3,2,1 -> 5 unique

### Procedure

Let's take example of n=6 and k=3:
Let max = 6 and min =1

First add all n-k elements to result while incrementing min
res = [1,2,3], min = 4,max=6

Then, add max and min to the result alternatively, decreasing max and increasing min when adding them

Steps:
res = [1,2,3,6], min = 4, max = 5

res = [1,2,3,6,4], min = 5, max = 5

res = [1,2,3,6,4,5], min = 5, max = 4

credits: [@piyushkumar1993](/u/piyushkumar1993)
# Code
```java []
class Solution {
    public int[] constructArray(int n, int k) {
        int[] res = new int[n];
        int left = 1, right = n;

        // 1,2,3... 
        for ( int i=0; i < (n-k); i++ ) {
            res[i] = left++;
        }

        // maximum element at (n-k)th index
        res[n-k] = right--;

        boolean add_left = true;
        
        // for index b/w [n-k+1,n) -> alternate b/w min and max
        for ( int i=n-k+1; i < n; i++ ) {
            res[i] = (add_left) ? left++ : right--;
            add_left = !add_left;
        }

        return res;
    }
}
```

```cpp []
class Solution {
public:
    vector<int> constructArray(int n, int k) {
        vector<int> res(n);

        int left = 1, right = n;

        // 1,2,3... 
        for ( int i=0; i < (n-k); i++ ) {
            res[i] = left++;
        }

        // maximum element at (n-k)th index
        res[n-k] = right--;

        bool add_left = true;
        
        // for index b/w [n-k+1,n) -> alternate b/w min and max
        for ( int i=n-k+1; i < n; i++ ) {
            res[i] = (add_left) ? left++ : right--;
            add_left = !add_left;
        }

        return res;
    }
};
```

```c []
int* constructArray(int n, int k, int* returnSize) 
{
    int* res = (int*) malloc( n * sizeof(int) );
    returnSize[0] = n;

    int left = 1, right = n;

    for ( int i=0; i < (n-k); i++ ) {
        res[i] = left++;
    }

    res[n-k] = right--;

    bool add_left = true;
    
    for ( int i=n-k+1; i < n; i++ ) {
        res[i] = (add_left) ? left++ : right--;
        add_left = !add_left;
    }

    return res;
}
```

```python3 []
class Solution:
    def constructArray(self, n: int, k: int) -> List[int]:
        res = []
        left, right = 1, n
        
        # 1,2,3... 
        for i in range(n-k):
            res.append(left)
            left += 1
        
        # maximum element at (n-k)th index
        res.append(right)
        right -= 1
        
        # for index b/w [n-k+1,n) -> alternate b/w min and max
        add_left = True
        for i in range(n-k+1, n):
            if add_left:
                res.append(left)
                left += 1
            else:
                res.append(right)
                right -= 1

            add_left = not add_left
    
        return res
```


```javascript []
/**
 * @param {number} n
 * @param {number} k
 * @return {number[]}
 */
var constructArray = function(n, k) {
    const res = new Array(n);
    let left = 1, right = n;

    // 1,2,3... 
    for ( let i=0; i < (n-k); i++ ) {
        res[i] = left++;
    }

    // maximum element at (n-k)th index
    res[n-k] = right--;

    let add_left = true;
    
    // for index b/w [n-k+1,n) -> alternate b/w min and max
    for ( let i=n-k+1; i < n; i++ ) {
        res[i] = (add_left) ? left++ : right--;
        add_left = !add_left;
    }

    return res;
};
```

```typescript []
function constructArray(n: number, k: number): number[] {
    const res = new Array<number>(n);
    let left = 1, right = n;

    // 1,2,3... 
    for ( let i=0; i < (n-k); i++ ) {
        res[i] = left++;
    }

    // maximum element at (n-k)th index
    res[n-k] = right--;

    let add_left = true;
    
    // for index b/w [n-k+1,n) -> alternate b/w min and max
    for ( let i=n-k+1; i < n; i++ ) {
        res[i] = (add_left) ? left++ : right--;
        add_left = !add_left;
    }

    return res;
};
```
