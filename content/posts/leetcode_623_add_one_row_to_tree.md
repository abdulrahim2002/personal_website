---
date: '2025-07-16T15:57:05+05:30'
math: true
author: abdul
title: 'Leetcode 623: Add one row to the tree'
---

# Level order traversal

- traverse down the levels until we are 1 level above the level to be replaced.
- for each node in the level obtained above, set left and right node value to `val`, and the original leftSubtree and rightSubtree become left and right links of newly inserted left and right node respectively

```typescript []
function addOneRow( root: TreeNode|null, 
                    val: number, depth: number): TreeNode|null {
    
    if ( depth <= 1 ) {
        return new TreeNode( val, root, null );
    }

    let que = [ root ];

    for ( let d=1; que.length && d < depth-1; d++ ) {
        const LVL = que.length;
        for ( let j=0; j < LVL; j++ ) {
            const node = que.shift();
            if ( node.left ) que.push( node.left );
            if ( node.right ) que.push( node.right );
        }
    }

    while ( que.length ) {
        const   node = que.shift(),
                leftSubtree = node.left,
                rightSubtree = node.right;
        node.left = new TreeNode( val, leftSubtree, null );
        node.right = new TreeNode( val, null, rightSubtree );
    }

    return root;
};
```

- Time: $$ O(n) $$
- Space: $$ O(n) $$

# Recursive delegation

The idea here is that if we need to update the `dth` level at node, then we need to update the `(d-1)th` level from `node.left` and `node.right`.

What are the stopping conditions?

- The current node is null -> just return null. The tree cannot be changed
- The depth is 1 -> make new node as root and put the current root in left child
- depth is 2 -> this means that we are 1 level above node-line to be changed. We insert the new nodes as child to current node, and the children are made grandchildren of current node. This way we make the required changes for current node only.

```typescript []
function addOneRow( node: TreeNode|null, 
                    val: number, depth: number): TreeNode|null {

    if ( !node ) return null;
    
    if ( depth <= 1 ) {
        return new TreeNode( val, node, null );
    }

    if ( depth == 2 ) {
        const leftSubtree = node.left, rightSubtree = node.right;
        node.left = new TreeNode( val, leftSubtree, null );
        node.right = new TreeNode( val, null, rightSubtree );
        return node;
    }

    addOneRow( node.left, val, depth-1 );
    addOneRow( node.right, val, depth-1 );

    return node;
};
```

- Time: $$ O(n) $$
- Space: $$ O(log\ n) $$
