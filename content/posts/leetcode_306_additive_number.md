---
date: '2025-07-16T00:50:40+05:30'
draft: false
title: 'Leetcode 306: Additive number'
author: abdul
math: true
---

Here's my explanation for the [leetcode 306: Additive Number problem](https://leetcode.com/problems/additive-number/description/)

## Additive Number Problem

### Problem Recap

An **additive number** is a string of digits where the sequence of numbers formed by splitting the string satisfies the condition that each number (after the first two) is the sum of the two preceding numbers. 

#### Examples:
- `"112358"` is additive because the sequence is `1, 1, 2, 3, 5, 8`, and:
  - `1 + 1 = 2`
  - `1 + 2 = 3`
  - `2 + 3 = 5`
  - `3 + 5 = 8`

- `"199100199"` is additive because the sequence is `1, 99, 100, 199`, and:
  - `1 + 99 = 100`
  - `99 + 100 = 199`

### Solution Approach

The solution uses a **backtracking approach** to try all possible splits of the string into sequences of numbers and checks if any of these sequences form an additive sequence.

### Detailed Explanation

#### Helper Function `isValid`

This recursive function checks if the remaining part of the string `s` forms a valid additive sequence given the first two numbers `a` and `b`.

- **Base Case**: If the remaining string `s` is empty, it means we've successfully formed an additive sequence, so return `true`.
  
- **Recursive Step**:
  1. Compute the sum of `a` and `b` and convert it to a string `sum`.
  2. Check if the remaining string `s` starts with `sum`:
     - If not, the sequence is invalid → return `false`.
     - If yes, recursively check the next part of the string with the new pair `(b, sum)` and the remaining string after removing `sum`.

### Main Function `isAdditiveNumber`

1. **Initialization**: Get the length of the input string `num`.
2. **Nested Loops**:
   - The outer loop (`i`) determines the end index of the first number `a` (from index `0` to `i`).
   - The inner loop (`j`) determines the end index of the second number `b` (from index `i` to `j`).
3. **Leading Zero Check**:
   - Skip any splits where `a` or `b` have leading zeros unless they are exactly `"0"`. 
     - Example: `"02"` is invalid, but `"0"` is valid.
4. **Validation**:
   - For each valid pair `(a, b)`, call `isValid` to check if the remaining part of the string forms a valid additive sequence starting with `a` and `b`.
   - If `isValid` returns `true`, immediately return `true` from the main function.
5. **Final Check**:
   - If no valid sequence is found after all possible splits, return `false`.

## Example Walkthrough

Let's walk through the example `num = "112358"`:

1. **First Iteration (`i = 1`, `j = 2`)**:
   - `a = "1"`, `b = "1"`.
   - No leading zeros → proceed.
   - `isValid(1, 1, "2358")`:
     - Sum of `1 + 1 = 2`.
     - Check if `"2358"` starts with `"2"` → Yes.
     - Recursively call `isValid(1, 2, "358")`:
       - Sum of `1 + 2 = 3`.
       - Check if `"358"` starts with `"3"` → Yes.
       - Recursively call `isValid(2, 3, "58")`:
         - Sum of `2 + 3 = 5`.
         - Check if `"58"` starts with `"5"` → Yes.
         - Recursively call `isValid(3, 5, "8")`:
           - Sum of `3 + 5 = 8`.
           - Check if `"8"` starts with `"8"` → Yes.
           - Recursively call `isValid(5, 8, "")`:
             - Empty string → return `true`.
   - Since `isValid` returned `true`, the main function returns `true`.

## Edge Cases

- **Leading Zeros**: Correctly skips invalid splits (e.g., `"02"` unless it's `"0"`).
- **Single Digit**: If input length < 3, returns `false`.
- **Large Numbers**: Uses `parseInt`, but `BigInt` is better for very large numbers to avoid precision issues.

## Time Complexity

- Nested loops: O(n²), where `n` is the string length.
- `isValid` function: O(n) per pair `(a, b)`.
- **Overall**: O(n³), feasible for reasonably sized strings.

## Space Complexity

- O(n) due to recursion stack in the worst case.

# Code
```javascript []
var isAdditiveNumber = function(num) {
    const isValid = (a, b, s) => {
        if (s.length === 0) return true;
        const sum = (a + b).toString();
        return s.startsWith(sum) && isValid(b, parseInt(sum), s.slice(sum.length));
    };

    const n = num.length;
    for (let i = 1; i < n; i++) {
        for (let j = i + 1; j < n; j++) {
            const a = num.slice(0, i);
            const b = num.slice(i, j);
            if ((a.startsWith('0') && a.length > 1) || (b.startsWith('0') && b.length > 1)) continue;
            if (isValid(parseInt(a), parseInt(b), num.slice(j))) return true;
        }
    }
    return false;
};
```


```javascript [TLE approach]
var isAdditiveNumber = function(num) {
    let N = num.length;

    let check = ( a ) => {
        /** check if numbers in an array form fibonacci seqence **/
        let A = a.length; if ( A < 3 ) return false;
        for ( let i=2; i < A; i++ )
            if ( Number(a[i]) != Number(a[i-1]) + Number(a[i-2]) ||
                 ( a[i].length > 1 && a[i].startsWith('0') ) ||
                 ( a[i-1].length > 1 && a[i-1].startsWith('0') ) ||
                 ( a[i-2].length > 1 && a[i-2].startsWith('0') )
               )
                return false;
        return true;
    };

    let bt_search = ( i, cur = [] ) => {
        if ( i > N )
            return false;
        if ( i === N && check(cur) )
            return true;

        // num[i] starts a new number, explore path
        cur.push( num[i] );
        let new_start = bt_search( i+1, cur );
        if ( new_start ) return true;

        // backtrack, num[i] continues the previous number
        cur.pop();
        if ( i > 0 ) {
            cur[ cur.length-1 ] += num[i];
            let add_prev = bt_search( i+1, cur );
            if ( add_prev ) return true;
        }

        return false;
   };

   return bt_search( 0 );
};

```
