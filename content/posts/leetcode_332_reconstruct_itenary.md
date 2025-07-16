---
date: '2025-07-16T15:35:54+05:30'
title: 'Leetcode 332: Reconstruct Itinerary. Eulerian path and Dfs'
math: true
---

## DFS intuition

First we must build a graph where airports are vertices and  an edge `airport1 -> airport2` denotes that the person travelled from `airport1 -> airport2`

To find a valid sequence of `vertices/airports` **that covers all the ``edges/tickets``**, we traverse the graph using depth first search like traversal. The idea is:

- At each vertex, move to the next lexicographically smallest vertex.
- delete the edge used 

To maintain lexicographical order, we sort the neighbours of each vertex lexicographically.

Algorithm:
Step 1: Build an adjacency list, where the neighbours of each node are sorted lexicographically

Step 2: Traverse using DFS starting from "JFK" airport. Remove edges as you move through the graph.

#### Incorrect approach

``` javascript []
/**
 * @param {string[][]} tickets
 * @return {string[]}
 */
var findItinerary = function(tickets) {
    const graph = {};

    for ( const [from, to] of tickets ) {
        if ( !graph[from] ) graph[from] = [];
        graph[from].push(to); 
    }

    for ( const node of Object.keys(graph) )
        graph[node].sort();

    const path = [];
    
    const dfs = ( node = "JFK" ) => {
        path.push(node);

        const to_where = graph[node] || [];

        while ( to_where.length ) {
            // remove the edge and visit the next smallest
            dfs( to_where.shift() );
        }
    };

    dfs();
    return path;
};
```

### Why it does not work:

Consider this example:  [["JFK","KUL"],["JFK","NRT"],["NRT","JFK"]]

```
+---+     +---+
|JFK|---->|KUL|
+---+     +---+
 | ^
 | |
 v |
+---+
|NRT|
+---+


path = []

# algorithm starts at JFK
@JFK -> add(JFK) 
    neighbours -> [KUL, NRT]
    explore(KUL) and remove edge JFK->KUL          ----(call 1)


graph becomes:                       path = [JFK]

+---+     +---+          
|JFK|     |KUL|
+---+     +---+
 | ^
 | |
 v |
+---+
|NRT|
+---+

@KUL -> add(KUL)
        there are no neighbours so return to call(1)

graph becomes:                  path = [JFK, KUL]

+---+     +---+         
|JFK|     |KUL|
+---+     +---+
 | ^
 | |
 v |
+---+
|NRT|
+---+


# So you see, we are again at JFK, even though there 
# is no edge from KUL to JFK
@JFK again
    neighbours now -> [NRT]
    explore(NRT) and remove edge JFK->NRT          ----(call 2)

+---+     +---+          
|JFK|     |KUL|
+---+     +---+
   ^
   |
   |
+---+
|NRT|
+---+

@NRT -> add(NRT)
    neighbours -> [JFK]
    explore(JFK) and delete NRT -> JFK          --- call(3)

graph becomes:
+---+     +---+                 path = [JFK, KUL, NRT]
|JFK|     |KUL|
+---+     +---+
   
    
    
+---+
|NRT|
+---+

@ JFK -> add(JFK)
        no neighnour so return to call(3)

graph becomes:
+---+     +---+                 path = [JFK, KUL, NRT, JFK]
|JFK|     |KUL|
+---+     +---+
   
    
    
+---+
|NRT|
+---+

@NRT (call 3) -> no more neighbours so return to call 2

@JFK (call 2) -> no more neighbours so algorithm ends
```

The final path is: ```[JFK,KUL,NRT,JFK]```

If we look at our tickets again: ```[[JFK, KUL],[JFK, NRT],[NRT, JFK]]```

We go from `JFK` to `KUL` using the ticket `[JFK, KUL]`. Then we move from `KUL` to `NRT` even though there is no direct ticket `[KUL, NRT]`.

From our dry run, we saw how the algorithm transported us back from `KUL` to `JFK`, by magically teleporting us.

# Correct approach

So how to fix the problem above. The fix is simple, **we shall only add a vertex, if all subgraph containing that vertex has been fully explored**. This approach ensures that we are only using continous links.

**This code is just 2 lines away from the dfs code above.** I added comments on what changed and why. 

```javascript []
/**
 * @param {string[][]} tickets
 * @return {string[]}
 */
var findItinerary = function(tickets) {
    const graph = {};

    for ( const [from, to] of tickets ) {
        if ( !graph[from] ) graph[from] = [];
        graph[from].push(to); 
    }

    for ( const node of Object.keys(graph) )
        graph[node].sort();

    const path = [];
    
    const dfs = ( node = "JFK" ) => {
        const to_where = graph[node] || [];
        while ( to_where.length )
            dfs( to_where.shift() );
       
        path.push(node); // add node only after all 
                        // the subgraph has been explored
    };

    dfs();

    // we changed the order of insertion, and 
    // we must reverse the path to obtain the original
    // itinerary
    return path.reverse();
};
```

### Complexity

- Time: $$O( E )$$
- Space: $$ O( V*E ) $$ // for storing the graph 

### Additional info:
An Eulerian trail or Euler walk, in an undirected graph is a walk that uses each edge exactly once. There are mainly 2 algorithms to solve  this problem: Fleury's algorithm, Hierholzer's algorithm. [source: https://en.wikipedia.org/wiki/Eulerian_path]

What we did above is a varient of Heirholzer's algorithm.

