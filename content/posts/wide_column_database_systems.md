---
date: 2025-10-01T09:26:49.794Z
layout: post
title:      "Widecolumn database systems"

subtitle:   "In this article, we try to find out the tradeoffs offered
by wide column databases and when these type of systems might be useful"

description: "Widecolumn database systems provide many benefits over
traditional SQL based relational databases such as improved availability
and high throughput. In this article, let's try to understand them in
detail."

category: ["system design"]
tags: ["software engineering", "design"]
author: abdulrahim
---


## Introduction

Wide column database systems store data in column families.  Below is a
small example of a NoSQL database:

![](https://i.ibb.co/63Z3pgf/Screenshot-from-2025-10-01-15-02-33.png)


Although, these relations look like tables, but they're not actually
tables. This is because, we cannot make arbitrary selections based on
non-primary columns. For example, the following query will fail:

![](https://i.ibb.co/0j2HPHCS/Screenshot-from-2025-10-01-15-02-18.png)

Therefore, similar to key-value stores, we can only query by using
primary keys.

A primary key in a wide column database consists of one or more
partition keys and zero or more clustering keys (or sorting keys).

![](https://i.ibb.co/WWdnh1KD/Screenshot-from-2025-10-01-18-52-38.png)




