---
date: '2025-07-16T16:02:05+05:30'
math: true
author: abdul
title: 'Leetcode 653: 2 sum with a BST'
---

I use `hcreate()`, `hdestroy()` and `hsearch()` from C standard library
for hashmap implementation.

```c []
#define _GNU_SOURCE
#include <search.h>
#include <errno.h>
#define MAX_SIZE 1e4
#define KEY_SIZE 20

ENTRY* create_entry_set_number( int data );
bool dfs( struct TreeNode*, int );

bool findTarget(struct TreeNode* root, int k) 
{
    hcreate( MAX_SIZE );
    const bool res = dfs( root, k );
    hdestroy();
    return res;
}

bool dfs( struct TreeNode* node, int target ) 
{
    if ( !node ) return false;

    const ENTRY* required = create_entry_set_number( target - node -> val );
    const ENTRY* cur_node = create_entry_set_number( node -> val );

    if ( hsearch( *required, FIND ) ) 
        return true;
    else 
        hsearch( *cur_node, ENTER );
    
    return dfs( node -> left, target ) || dfs( node -> right, target );
}

ENTRY* create_entry_set_number( int data ) 
{
    ENTRY* item = ( ENTRY* ) malloc( sizeof(ENTRY) );
    if ( !item )  exit(-ENOMEM);
    *item = ( ENTRY ) { .key = NULL, .data = NULL };
    item -> key = (char*) reallocarray( item->key, KEY_SIZE, sizeof(char) );
    sprintf( item -> key, "%d", data );
    return item;
}
```
