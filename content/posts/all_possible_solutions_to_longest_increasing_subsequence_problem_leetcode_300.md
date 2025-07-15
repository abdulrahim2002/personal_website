---
date: '2025-07-16T00:44:05+05:30'
draft: false
title: 'All possible solutions to longest increasing subsequence problem: leetcode 300'
author: abdul
---

Here are all possible solutions I could come up with for the longest
increasing subsequence problem. [leetcode 300](https://leetcode.com/problems/factorial-trailing-zeroes/description/)


# Approach 1: Generate all possible increasing subsequences

We will keep track of a subsequence in an array named `cur_subsequence` or `cur_sub`. For each element (`a[i]`)  we have the following 2 options:

1. Add the element to the end of current subsequence.

    *Note: The current element can only be included if 
    either the current subsequence is empty or the last
    element of the current subsequence is smaller than 
    the current element. This is important to maintain 
    the increasing subsequence property.*
2. Ignore the current element and explore without `a[i]`

- Time complexity: $$ O(2^N) $$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity: $$O(N)$$ *recursion stack + auxilary array*
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

```javascript []

var lengthOfLIS = function(a) {
    let A = a.length;
    // maximum length of a subsequence seen so far
    let max_len = 1;

    let bt_search = ( i, cur_sub = [] ) => {
        if ( i >= A ) {
            max_len = Math.max( max_len, cur_sub.length );
            return;   
        }

        // either select the ith element, or ignore it.
        // a[i] can be taken when either subsequencee is empty or 
        // last inserted element is smaller 
        if ( !cur_sub.length || cur_sub[ cur_sub.length-1 ] < a[i] ) {
            cur_sub.push( a[i] );
            bt_search( i+1, cur_sub );
            cur_sub.pop(); // backtrack
        }

        // explore without a[i] included
        bt_search( i+1, cur_sub );
    };

    bt_search(0);
    return max_len;
}

```

# Approach 2 (Version 1) : Generating all possible subsequences, but change the structure of the recursion to allow memoization

Currently, we are keeping track of the current subsequence in an array `cur_sub` which makes it difficult for us to implement memoization. Notice that we only need previously selected element (to compare if current element is larger) and current length (to find the subsequence length). Hence, we will only use 3 variables: index, previous element index, current length.

- Time complexity: $$ O( N^2 ) $$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity: $$O( N^3 )$$
<!-- Add your space complexity here, e.g. $$O(n)$$ -->

```javascript []
var lengthOfLIS = function(a) {
    /**
        Write a recurrence relation. Either you take the
        element @i or your ignore it. In either case, 
        your pick the maximum length.
        
        generating all possible increasing subsequences,  
        but not storing the subsequence and using only 3   
        variables. 
        for each call(index, prev_selected, cur_length) 
        we try to find the maximum length we can form by
        either selecting or rejecting a[i]
        This configuration is easy to memoize. 
    **/
    let A = a.length;
    let memo = new Map();

    let bt_search = ( i, prev, cur_len = 0 ) => {
        
        // inputs of the function are memoized by turning
        // them into a String `key`
        let key = JSON.stringify( [i, prev, cur_len] );

        if (memo.has(key)) return memo.get(key);

        if ( i >= A ) return cur_len;
        
        let take = -1, not_take = -1;

        // a[i] can be taken if prev=null / cur_len=0 
        // or prev < a[i]
        if ( cur_len == 0 || prev < a[i] )
            take = bt_search( i+1, a[i], cur_len+1 );
        
        // backtrack. do not take a[i]
        not_take = bt_search( i+1, prev, cur_len );

        let res =  Math.max( take, not_take );

        memo.set(key, res);
        return memo.get(key);
    };

    return  bt_search(0);
}
```

# Approach 2 (Version 2): Yet another recursive solution, but with better memoization

This solution memoizes using only: index of current element (`i`), index of previously selected element (`prev_i`).



The memoization is done in a matrix of size $$N$$X$$(N+1)$$.

- Time complexity: $$ O( N^2 ) $$
<!-- Add your time complexity here, e.g. $$O(n)$$ -->

- Space complexity: $$O( N^2 )$$
<!-- Add your space complexity here, e.g. $$O(n)$$ -->


```javascript []
var lengthOfLIS = function(a) {
    /** Approach 2 (version 2): Instead of storing 3 variables, just 
        store 2 variables. This will enable memoization table to be 
        an NxN matrix.  **/
    let A = a.length;
    let memo = new Array( A ).fill(null).map( 
            () => new Array( A+1 ).fill( null ) 
        );
    
    let bt_search = ( i, prev_i = -1 ) => {
            // y is remapped since prev_i is in range [-1,n-1]
            // but our array ranges is in range [0, n]        
            let x = i, 
            y = prev_i + 1;
        
        if ( i >= A ) return 0;
        
        if ( memo[x][y] !== null ) return memo[x][y];        
        
        let take = -1, not_take = -1;

        // a[i] can be taken if prev_i = -1 / cur_len=0 or 
        // a[prev_i] < a[i]
        if ( prev_i == -1 || a[prev_i] < a[i] )
            take = 1 + bt_search( i+1, i );

        // backtrack. do not take a[i]. The length remains same
        not_take = bt_search( i+1, prev_i );

        let res =  Math.max( take, not_take );
        
        memo[x][y] =  res;
        
        return memo[x][y];
    };

    return  bt_search(0);
}

```

# Approach 3: Dynamic programming

Declare a `lis`(longest increasing subsequence) array, where `lis[i]` denotes the length of longest increasing subsequence starting at `i` and ending at the end of the array. Fill this table from second last element towards first element.

To find `lis[i]` for an element `a[i]` use the following algorithm:

        For each i <- n-2 - 0
            // find if index i can form an increasing subsequence
            // with any element on the right of it
            max_len = 1
            For each j <- i+1 - n
                if ( a[i] < a[j] ) // a[j] is a potential connection point
                    max_len = MAX( max_len, 1 + lis[j] )
            lis[i] = max_len

The basic intuition behind it is, that we explore all elements to the right of `a[i]` which can connect with `a[i]` to form an  increasing subsequence. The length of such a subsequence is given by:

1 (length of `a[i]`) + length of longest subsequence from `a[j]` up until the end of the array

which is same as:
1 + `lcs[j]`



- Time: $$O(n^2)$$
- Space: $$O(n)$$


```javascript []
let MAX = Math.max;

var lengthOfLIS = function(a) {
    /** Appraoch 3: Build the dp table manually using loops **/
    let A = a.length;
    let lis = new Array( A ).fill(1);
    
    // global maximum, subsequence length from a[i] until end
    let gmax = 1;
    
    for ( let i = A-2; i >= 0; i-- ) {
        let max_len = 1;
        for ( let j = i+1; j < A; j++ ) {
            if ( a[i] < a[j] ) 
                max_len = MAX( max_len, 1+lis[j] );
        }
        lis[i] = max_len;
        gmax = MAX( max_len, gmax );
    }

    return gmax;
}
```

# Approach 4: Build an auxilary array, such that the minium elements are always at the end of it

Here, we are basically trying to kick out large elements and inserting small elements whereever possible, so that new incoming elements feel welcome attaching themselves to the existing subsequence.


Build a auxilary array  using the following algorithm:

        subsequence = []

        For each i <- 0 to A-1
            if subsequence.last_element < a[i]
                subsequence.push( a[i] )
            else {
                replace the largest element smaller than or equal to a[i]
                in subsequence using binary search.
            }

        the length of the subsequence is the length of the largest increasing
        subsequence.

- Time: $$O(n\ log n)$$ // n for iteration, and for each iteration binary search O(log n)
- Space: $$O(n)$$ // auxilary array

**Note that the array that we actually build here does not represent a valid increasing subsequence. Nor is it the longest increasing subsequence, we just insert the elements using a particularly methodology which ensures its validity**

```javascript []
let MAX = Math.max;
let FLOOR = Math.floor;

var b_search = ( a, target ) => {
    // return the index pf the number >= target
    let i = 0, j = a.length-1;
    while ( i <= j ) {
        let mid = i + FLOOR( (j-i)/2 );
        if ( a[mid] == target ) return mid;
        else if ( a[mid] < target ) i = mid+1;
        else j = mid-1;
    }
    return i;
};

var lengthOfLIS = function(a) {
    /** Approach 4: The idea is to keep the minium 
        elements, such that validity of lis(longest 
        increasing subsequence) for new incoming 
        elements can be calculated quickly 
    **/
    let A = a.length;
    let sub = [ a[0] ];

    for ( let i=1; i < A; i++ ) {
        
        let lst = sub.length-1;
        
        if ( sub[ lst ] < a[i] ) sub.push( a[i] );
        else {
            // find a "write index" where we replace a[i]
            let wi = b_search( sub, a[i] );
            sub[wi] = a[i];
        }
    }

    return sub.length;
}

```

# Approach 5: In place algorithm for approach 4

- Time: $$O(n\ logn)$$
- Space: $$O(1)$$ 

```javascript []
let MAX = Math.max;
let FLOOR = Math.floor;

/* this version is for searching within the same array
    starting and ending position are requirede */
var b_search = ( a, start, end, target ) => {
    // return the index pf the number >= target
    let i = start, j = end;
    while ( i <= j ) {
        let mid = i + FLOOR( (j-i)/2 );
        if ( a[mid] == target ) return mid;
        else if ( a[mid] < target ) i = mid+1;
        else j = mid-1;
    }
    return i;
};

var lengthOfLIS = function(a) {
    /***
    Approach 5: Same as the above approach, but in place
    Hence saving auxilary space. The idea is to keep a
    variable to mark the end of the subsequence in a itself.
    **/
    let A = a.length;

    let S = 1; // variable to mark the end of the subsequence
    for ( let i=1; i < A; i++ ) {
        if ( a[S-1] < a[i] ) {
            a[S] = a[i];
            S++;
        } 
        else {
            // find a "write index" where we replace a[i]
            let wi = b_search( a, 0, S-1, a[i] );
            a[wi] = a[i];
        }
    }

    return S;
}
```
