---
date: '2025-07-16T15:45:27+05:30'
math: true
author: abdul
title: 'Leetcode 467: Unique Substrings in Wraparound String (Solution
with intuition)'
---

1. for each character, find the length of the longest continous substring that ends with that character
2. for each character the number of _continous substrings_ that end in that character = length of the longest continous substring that ends in that character ( as obtained in step 1 )

<img src="https://assets.leetcode.com/users/images/92a07d59-0d42-4866-9eb8-dc5b86b2963d_1748717768.2913718.png" alt="explanation" width="300" height="200">


# Code
```typescript []
function findSubstringInWraproundString(s: string): number {
    let curStreak = -1,
        lenMap = new Array(26).fill(0);

    for ( let i=0; i < s.length; i++ ) {
        if ( i > 0 && isContinous( s[i-1], s[i] ) ) curStreak++;
        else curStreak = 1;

        lenMap[ CODE(s[i]) ] = Math.max( lenMap[ CODE( s[i] ) ], curStreak );
    }

    return lenMap.reduce( (sum, curLen) => sum += curLen, 0 );
};

function isContinous( c1: string, c2: string ) {
    return ( CODE(c2) - CODE(c1) + 26 ) % 26 == 1;
}

function CODE( c: string ) {
    return c.charCodeAt(0) - 'a'.charCodeAt(0);
}
```
