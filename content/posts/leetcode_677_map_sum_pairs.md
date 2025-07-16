---
date: '2025-07-16T16:09:06+05:30'
draft: true
math: true
author: abdul
title: 'Leetcode 677: Map sum pairs solution'
---

We build a simple Tri where each node's value is the sum of all words in the Tri that have same prefix as current node.

Let me explain with a simple example. Suppose, we have an empty Tri. Insert `"apple"` with `value=3`. While inserting a key, we shall increase values of all intermediate nodes.

Here's how to Tri looks like after insertion:

![image.png](https://assets.leetcode.com/users/images/72dcefb6-a178-4bdf-8a51-b036825d56f3_1751391227.1387794.png)

Now let's insert `"app"` with `value=2`. Please note that we shall increase the value of intermediate nodes while inserting app. Therefore, the first 3 nodes will have `value=5`.

![image.png](https://assets.leetcode.com/users/images/88bb2e38-5e94-4ea1-9307-b265e70d70b1_1751391429.616798.png)

Now if you observe carefully. The query for the prefix `ap` will end up at the first `p` node from start, which will have `value=5`. This is the desired behaviour as both `"app"` and `"apple"` share prefix `"ap"`, hence we return sum of their values.

So far so good. But what happens, when we want to update the value for a word. For example, I want to change the value for `"apple"` form  `3` to `1`.

To solve this problem, first we need to figure out how can we delete the value of `"apple"`. Fortunately, this can be simply done by inserting `negative of old value`. For example, we can delete the value of apple by inserting `-1 * old_value = -3`.

![image.png](https://assets.leetcode.com/users/images/ee78394d-cee5-4af5-a8df-854b24e95fcd_1751391921.8674603.png)

Notice that now queries like: `"appl"` will give result=`0` since there is no longer any word in the Tri with prefix `"appl"`.

Now we can just reinsert the new value for `"apple"` i.e. `1`.

![image.png](https://assets.leetcode.com/users/images/9278992d-1483-4886-90f5-04208a5e5c0a_1751392457.62204.png)


Note that queries like `"ap"` will return `3` which is correct since, `"apple" -> 1` and `"app" -> 2`.

While update the value of keys, we can just combine the steps of deleting and reinserting into one by just inserting ` -(old value) + (new value)`.

# Complexity
- Time complexity: $$O( prefix.length )$$ for `sum()` and $$O(word.length)$$ for `insert()`

- Space complexity: $$O(n)$$

# Code
```typescript []
class MapSum {
    public map = new Map<string, number>();
    public tri = new Tri();
    insert(key: string, val: number): void {
        let oldVal = this.map.get(key)??0;
        this.map.set(key, val);
        this.tri.insert(key, -oldVal + val);
    }
    sum(prefix: string): number {
        return this.tri.query(prefix);
    }
}

class TriNode {
    public children = new Map<string,TriNode>();
    public val: number = 0;
}

class Tri {
    public root = new TriNode();
    insert(word: string, value: number) {
        let node = this.root;
        for ( let i=0; i < word.length; i++ ) {
            if ( !node.children.has(word[i]) )
                node.children.set(word[i], new TriNode());
            node = node.children.get(word[i]);
            node.val += value;
        }
    }
    query(prefix: string) {
        let node = this.root;
        for ( let i=0; i < prefix.length; i++ ) {
            if ( !node.children.has(prefix[i]) )
                return 0;
            node = node.children.get(prefix[i]);
        }
        return node.val;
    }
}
```


```python3 []
class TrieNode:
    def __init__(self):
        self.children = dict()
        self.val = None
        self.is_leaf = False
        
    def set_val(self, val):
        self.val = val
        
    def set_leaf(self):
        self.is_leaf = True
        
        
class MapSum:

    def __init__(self):
        self.trie = TrieNode()

    def insert(self, key: str, val: int) -> None:
        curr = self.trie
        for ch in key:
            if ch not in curr.children:
                curr.children[ch] = TrieNode()
            curr = curr.children[ch]
        curr.set_val(val)
        curr.set_leaf()

    def sum(self, prefix: str) -> int:
        curr = self.trie
        sum_prefix = 0
        
        for ch in prefix:
            if ch not in curr.children:
                return 0
            curr = curr.children[ch]
        if curr.is_leaf:
            sum_prefix += curr.val
        queue = collections.deque([curr])
        while queue:
            curr = queue.popleft()
            for child, node in curr.children.items():
                queue.append(node)
                if node.is_leaf:
                    sum_prefix += node.val
            
        return sum_prefix


# Your MapSum object will be instantiated and called as such:
# obj = MapSum()
# obj.insert(key,val)
# param_2 = obj.sum(prefix)
```
