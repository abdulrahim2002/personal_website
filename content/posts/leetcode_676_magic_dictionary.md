---
date: '2025-07-16T16:17:29+05:30'
draft: false
math: true
author: abdul
title: 'Leetcode 676: Magic dictionary'
---

For each word, we basically store all versions of it after removal of 1 character. For example,

```
hello ->
ello -> removed h@0
hllo -> removed e@1
helo -> removed l@2
helo -> removed l@3
hell -> removed o@4
```

We can store: `wordAfterRemoval,indexOfRemoval` in hashmap. So whenever we search for a word like: `hexlo` then we can try removing it's 2nd index and search: `helo,2` in the map, which we will find.

In the value we can store the removed character. For example, store key=`helo,2` with value=`l` to indicate that `l` was removed.

So when you match a word like `hexlo`. Try to search `helo,2` in the hashmap. It gives value=`l` which is `!='x'` i.e. the removed character in `hexlo`.

However, there is one problem with this approach. When you add 2 words like: `hello` and `hallo` in the map. Then `hllo,1` will give `e` in the first insertion and `a` in the second. The second overides the first so `hello` is forgetten by the structure. To avoid this issue, we store both of them (`e`, `a`) in a list.

---

```typescript []
class MagicDictionary {
    private map = new Map<string,string[]>();

    buildDict(dictionary: string[]): void {
        for ( const word of dictionary ) {
            for ( let i=0; i < word.length; i++ ) {
                
                const wordAfterRemoval = word.slice(0, i) + word.slice(i+1);
                // key stores wordAfterRemoval,indexOfRemoval as value
                const key = `${wordAfterRemoval},${i}`;

                if ( !this.map.has(key) )
                    this.map.set(key,[]);

                // values have the removed character
                this.map.get(key).push( word[i] )
            }
        }
    }
    search(word: string): boolean {
        for ( let i=0; i < word.length; i++ ) {
            const wordAfterRemoval = word.slice(0, i) + word.slice(i+1);
            const key = `${wordAfterRemoval},${i}`;
            if ( !this.map.has(key) ) continue;
            const removedChars = this.map.get(key);
            
            // If there was a word with removed character != word[i]
            for ( let j=0; j < removedChars.length; j++ ) {
                if ( removedChars[j] !== word[i] )
                    return true;
            }
        }

        return false;
    }
}
```


You can also embed the index information by replacing the removed word with `_` like in this pyton version:


```python3 []
class MagicDictionary:

    def __init__(self):
        self.map = {}

    def buildDict(self, dictionary: List[str]) -> None:
        for word in dictionary:
            for i in range(len(word)):
                key = word[0:i] + '_' + word[i+1:]
                
                if not key in self.map:
                    self.map[key] = []
                
                self.map[key].append(word[i])
        
    def search(self, searchWord: str) -> bool:
        for i in range(len(searchWord)):
            key = searchWord[0:i] + '_' + searchWord[i+1:]
            
            if key not in self.map: 
                continue
            
            for c in self.map[key]:
                if c != searchWord[i]:
                    return True
        
        return False

# Your MagicDictionary object will be instantiated and called as such:
# obj = MagicDictionary()
# obj.buildDict(dictionary)
# param_2 = obj.search(searchWord)
```
