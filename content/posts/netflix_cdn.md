---
date: 2025-09-24T13:24:34.935Z
layout: post
title: "Netflix CDN"
subtitle: Netflix needs to send a lot of data in the form of packets to its users. For this purpose, it utilizes a massic CDN...
description: Netflix sends a lot of data to its users. Transporting millions of data packets (video data) to each user to millions of users requires an increadibly scalable and robust system.

category: ["system design"]
tags: ["software engineering", "design"]
author: abdulrahim
---


## Introduction

You are netflix. Now your main servers are hosted in the USA, however,
you have users all over the world. Now suppose, a user from India
request a video (movie/TV etc.) that is hosted in a USA data center. To
serve the request, you need to transmit data all across the world, which
increases the users latency and makes up for a bad user experience. And
if there are millions of users in India, requesting data from USA, then
the network will become like red hot steel.

A content delivery network (CDN) is responsible for delivering content
to the users from a location close to them. It reduces data travel path,
hence decreasing latency for end users.  Netflix operates a massive CDN
called Open Connect for its video delivery. In this article, we
understand how it works.

## Fundamentals

The origin server is the server where the main data (source of truth)
resides. This may be the main database server or data center.

An edge server, stores a copy of data from origin server. It acts as a
read replica / catching server for the origin server. Edge servers are
typically located across many geographies so as to provide data to end
user as fast as possibel across multiple locations.

![](https://i.ibb.co/WNKZmFny/Screenshot-from-2025-09-24-19-06-41.png)

## Load balancing

In each country, usually, there are mulitple availability zones. For
example, amazon web services operate az-east, az-west and other
availability zones with the US. And for each country, there may be
multiple availability zones.

Requests are load balanced across 2 layers. First, for each geography,
there may be multiple availability zones that the current client can
fetch data from. Hence, the first layer of load balancer operates among
availability zones. The second layer of load balancer, operates among
multiple nodes with a particular availability zone.


![](https://i.ibb.co/JW1P6Kr9/Screenshot-from-2025-09-24-19-16-12.png)



## Caching layer

Netflix is a read heavy system because it does not contain user
generated content. Also the write frequency is much less than read
frequency. Keeping this in mind, netflix has implemented a write through
+ read through catching layer.

Netflix's caching layer is called "EV cache". Each read/write request
goes through one of many cache clients in an availability zones.
Whenever a write request is received. All clusters/nodes/replication
groups are updated simoultaniously along with the cache.

However, when a read request is received. It is allocated to a
particular cache client, which then talks to the nearest convinent node
and returns the data from it.

![](https://i.ibb.co/7tZcrW7W/Screenshot-from-2025-09-24-20-03-02.png)

Another interesting point to note is that EV cache is built on top of
[memcached](https://en.wikipedia.org/wiki/Memcached). And usually,
caching systems store data in RAM.

However, netflix caches store data not in RAM, but SSD hard disks. SSD's
are faster than traditional hard drives (HDD's) but still slower than
RAM. This setup allows them to reduce the costs.
