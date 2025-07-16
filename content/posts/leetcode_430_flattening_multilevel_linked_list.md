---
date: '2025-07-16T15:42:17+05:30'
draft: false
math: true
author: abdul
title: 'Leetcode 430: Flattening a multilevel linked list'
---


The idea is to keep 2 pointers. `trail` pointer and `cur` pointer. The
list is build in recursive function `build(trail, cur)` which returns
last node of the list we build.

`build` works as follows:
1. when there is no `child` node: simply connect `trail` and `cur` and advance both
2. when there is a `child` node, then recursively call itself with `build( trail = cur, cur = cur.child )`. The call would connect `cur` node with the `list in the next level`. It would return the last node in next level. Then we assign `trail = last node in next level` and `cur = cur.next in current level`. This ensures that the next iteration would connect `last node in next level` to `next node in current level`.
3. when `cur` becomes `null` `trail` is the last node in current level. `return trail`



# Code
```typescript []
function flatten(head: _Node | null): _Node | null {
    const   save: _Node = new _Node( -1 );
    build( save, head );
    if ( save.next ) save.next.prev = null;
    return save.next;
};

function build( trail: _Node | null, cur: _Node | null ): _Node | null {
    while ( cur ) {
        if ( !cur.child ) {
            trail.next = cur; cur.prev = trail;
            trail = trail.next; cur = cur.next;
        }
        else {
            trail.next = cur; cur.prev = trail;
            const saveNext = cur.next;
            const lastNodeFromChildList = build( cur, cur.child );
            cur.child = null;
            cur = saveNext;
            trail = lastNodeFromChildList;
        }
    }
    return trail;
}
```

# Complexity
- Time complexity:  $$O(n)$$

- Space complexity: $$O(n)$$ {recursion stack}

