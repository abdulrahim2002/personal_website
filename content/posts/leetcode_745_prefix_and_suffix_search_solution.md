---
date: '2025-07-16T16:30:15+05:30'
title: 'Leetcode 745: prefix and suffix search solution'
author: abdul
math: true
---

For each possible word, we ask the question: **What possible queries can lead to this word?**.

For example consider the word: `abd`. Below are the possible (prefix, suffix) queries that can return `abd`
```
(prefix, suffix)
a    abd
a    bd
a    d
ab  abd
ab  bd
ab  d
abd  abd
abd  bd
abd  d
```
Now, given word.length <= 7. Each word can at maximum generate `7*7=49` such pairs. Can we store all of them in a hashmap? Yes, because the total storage = `49 * number of words = O(n)` Which is acceptable for this problem.

What if there is already a value for (prefix,suffix) in current word. Shall we override it. We need the latest index right?


```python3 []
class WordFilter:
    def __init__(self, words: List[str]):
        self.map = {}

        for widx, w in enumerate(words):
    
            for i in range( len(w) ):
                prefix = w[:i+1]

                for j in range( len(w) ):
                    suffix = w[j:]

                    # for all possible prefix suffix queries that might route
                    # to current word. We set it in dictionary overriding previously
                    # inserted values to get the maximum index
                    key = prefix + '|' + suffix
                    self.map[key] = widx

    def f(self, pref: str, suff: str) -> int:
        key = pref + '|' + suff
        return self.map.get(key, -1)
```
