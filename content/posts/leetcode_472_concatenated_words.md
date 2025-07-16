---
date: '2025-07-16T15:59:09+05:30'
draft: true
math: true
author: abdul
title: 'Leetcode 472: Concatenated words'
---

consider the word "catsdogcats". We have the dictionary: `["cat","cats","catsdogcats","dog","dogcatsdog","hippopotamuses","rat","ratcatdogcat"]`

We iterate over the word: `catsdogcats` and at each iteration, we ask if the prefix is contained in dictionary.

If the prefix is in the dictionary, we recursively call the function on the remaining word (excluding matched prefix)

- is `c` in dictionary ?
- is `ca` in dictionary ?
- is `cat` in dictionary ? -> YES       call( `sdogcats` ) --[1]

Prefix `cat` matched so recursively call with `sdogcats`

- is `s` in dictionary ?
- is `sd` in dictionary ?
- .... 
- is `sdogcats` in dictionary ? NO

We exhausted the word so we return false. Nothing was matched

Back at the first call, this time we try to match `cats`
Return to [1]

- is `cats` in dictionary -> YES    recursively call on `dogcats`

- is `d` in dictionary ?
- is `do` in dictionary ?
- is `dog` in dictionary ? -> YES,   recursively call on `cats`

- is `c` in dictionary ?
- is `ca` in dictionary ?
- is `cat` in dictionary ? YES ,        recursively call(`s`) --[2]

- is `s` in dictionary ? NO -> word exhausted, return false;

Back at recursive call [2]

- is `cats` in dictionary ? YES         recursively call(``) i.e. empty string

When we reach empty string, it means whole of string can be constructed using words in dictionary. Hence return true.


Import considerations:

- The dictionary has all the words, which means that `catsdogcats` will match with itself completely. To avoid words matching with themselves, we tell the function to ignore the `catsdogcats` word to avoid matching it with itself.
- Also, we can memoize the results since, dictionary remains the same, only target word and ignore word changes. 


```typescript []
function findAllConcatenatedWordsInADict(words: string[]): string[] {
    const   dict = new Set<string>( words );
    return  words.filter( curWord => canChunk( curWord, curWord, dict ) );
};

function canChunk(  target: string, ignore: string, 
                    dict: Set<string>,
                    memo = new Map<string, boolean>() ): boolean {
    
    if ( !target.length ) return true;

    const key = target + ignore;
    if ( memo.has( key ) ) return memo.get( key );

    for ( let i=1; i <= target.length; i++ ) {
        const prefix = target.slice( 0, i );
        if (    prefix !== ignore &&
                dict.has( prefix ) && 
                canChunk( target.slice( i ), ignore, dict, memo ) 
            ) {
            memo.set( key, true );
            return true;
        }
    }
    memo.set( key, false );
    return false;
}
```
