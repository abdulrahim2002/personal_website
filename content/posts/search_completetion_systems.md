---
date: 2025-09-27T08:44:40.978Z
layout: post
title: "Search completetion systems"
subtitle: "When you start typing something on a search engine like
google. You will notice that many suggestions pop up! These are
populated by something called a search completetion system"
description: "When you type somthing on in a search engine, you will
notice few search suggestions pop up. These search suggestions are
populated by what is called a search completion system. In this article,
lets try to decode how a search completetion system works."
category: ["system design"]
tags: ["software engineering", "design"]
author: abdulrahim
---

## Introduction

When you type something on a search engine like google. You are
continously shown search suggestions, as you type.

![](https://i.ibb.co/Dhvj4z7/Screenshot-from-2025-09-27-14-31-20.png)

## API

As you type in the search box, behind the scenes, google continously
sends request to the backend. The requests might look like this:

```
GET /autocomplete?q=be

{
  // metadata such as user id etc for personalized suggestion
}
```

The backend responds with a list of suggestions, which might look something like:

```
200 OK

{
  "suggestions": [
    "best hotels near me",
    "beach destinations",
    "bears",
    "beauty products",
    "behavioral psychology",
    "bitcoin",
    "beetlejuice",
    "best laptop 2025"
  ]
}
```

These are then populated in a box on the user interface.

## How does it work

This kind of functionality can be implemented with a Tri. The idea is
that at each node in the Tri, we keep a list of top k most frequest
words having the prefix.

![](https://i.ibb.co/VYnt3qGB/Screenshot-from-2025-09-27-14-53-13.png)

This approach allows us to look up top k most frequest words with a
particular prefix in `O(length of prefix)` time.

For example, if i need to look up top k words for prefix "be" then I can
quickly iterate through the Tri to get the node that represents prefix "be".

But you might quickly figure out, that maintaining such a Tri across
millions of words will require a humoungous Tri and it is true.

We cannot reduce the space requirements of the Tri, but we can shard the
system so that we can store it across multiple nodes or data centres
for that matter.


For example, we can distribute Tri across 2 clusters. This way, all
queries with prefix="a" will be routed to first cluster. And all queries
with prefix="b" will be routed to second cluster.

Extending this approach, you can indefinately divide the Tri across
multiple nodes/clusters/availability zones/data centres etc.


![](https://i.ibb.co/h1snfgfY/Screenshot-from-2025-09-27-15-03-27.png)



## How are top k words populated in the Tri

Now the question is, how can we populate the top k words in the Tri for
each word. There are many approaches by which it can be done.

As a simple example, we could monitor queries in given intervals of time
(say 1 hour) and for each hour. We find, what are the most common words.
We can then regenerate/update the Tri with the new data. This allows the
system to be aware of real time events.

Machine learning approaches can also be applied in this context. For ML
perspective, this problem can be viewed as a next token prediction
problem. Hence, we can use transformer models which are known to excel
in this tasks.


## Conclusion

A search completetion, whilst looks simple, is an increadibly
complicated system under the hood. There are many methods that can be
employed to develop such a system, in this article, we just touched the
surface of what might an implementation look like.
