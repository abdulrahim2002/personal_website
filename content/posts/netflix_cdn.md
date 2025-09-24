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
