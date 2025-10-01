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


## Characterstics

Although, these relations look like tables, but they're not actually
tables. This is because, we cannot make arbitrary selections based on
non-primary columns. For example, the following query will fail:

![](https://i.ibb.co/0j2HPHCS/Screenshot-from-2025-10-01-15-02-18.png)

Therefore, similar to key-value stores, we can only query by using
primary keys.

A primary key in a wide column database consists of one or more
partition keys and zero or more clustering keys (or sorting keys).

![](https://i.ibb.co/WWdnh1KD/Screenshot-from-2025-10-01-18-52-38.png)

Wide column databases allow horizontal partitioning of data among
multiple nodes enabling horizontal scalability.

## Data modeling

One important point to consider is that wide column databases store data
in denormalized form. This means, that all data related to an object is
stored along with that object at the same place. 

![](https://i.ibb.co/Ngph8qDr/Screenshot-from-2025-10-01-19-07-23.png)

For example, in a traditional relational database schema. Consider an
entity user:

```
User:

Attributes: user_id (primary key), user_name
```

And an entity called comments:

```
Comments:

Attributes: comment_id (primary key), comment_text, user_id (Foreign key)
```

In this scheme, we can retrieve the comments made by a particular user using the
following query:

```sql
SELECT * FROM comments, user WHERE user_id = <given_id>
```

However, this does not work in wide column databases like cassandra.
This is because, they **do not support joins.**

We need to redefine the schema as follows:

```
Cassandra Schema for the above relations:


User:

Attributes:
    user_id,
    user_name,
    comments // all comments made by the current user in an appropriate data type
```

Now we should be able to retrieve the comments using the following
query:

```sql
SELECT comments from table_name with user_id = <given_user_id>
```

Since we no longer need to perform joins, retrieving data in wide column
databases is much faster and efficient.  However, storing data in a
denormalized form has its own problems. For example we  usually need to
store duplicate data when modeling data in denormalized form. This makes
it hard to keep copies in sync leading to data inconsistencies.

![](https://i.ibb.co/MxRjR2k7/Screenshot-from-2025-10-01-19-19-19.png)

Also, you need to model you data such that the column you want to search
over for a record (say user\_id) needs to be the partition key. For
example, suppose you need to run the following query:

```sql
SELECT * FROM table WHERE search_column = <value>
```

Now, you need to model your data such that search\_column is the
partition key.

But what if you need to search over multiple columns?

In that case, you would need to create another table which is campatible
with your query pattern. Below is a simple example illustrating this:

![](https://i.ibb.co/1fJRsgpF/Screenshot-from-2025-10-01-19-26-32.png)

Wide column databases provide much better write performance than
relational databases.

## Data partioning

Since partitioning is embeded into the data model through partition
keys. Horizontal scalability is natural to wide column databases. We can
scale up or down depending on the demand simply by adding removing
nodes.

![](https://i.ibb.co/hJDFT4kH/Screenshot-from-2025-10-01-19-37-19.png)

But what happens, when a node is added or removed. Is the data
redistributed across all nodes? No. This is not the case. For example,
cassandra uses consistent hashing technique to minimize the data
transfer in case of addition/removal of nodes.

