---
date: 2025-09-25T15:48:12.309Z
layout: post
title: "Designing an E commerce platform"
subtitle: "How do e commerce platforms like flipkart, amazon operate.
And how do they handle millions of users"
description: "E commerce platforms basically showcase products online
through images and provide users the ability to click and buy the
product they want..."

category: ["system design"]
tags: ["software engineering", "design"]
author: abdulrahim
---

## Functional requirements

1. search: The search functionality provides users with the ability to
   list all products that best match their query. We should also be able
   to tell weather we are able to deliver a product to the users
   location or not. For example, if a product cannot be shipped to the
   user, it should be communicated to the user.
2. cart: users should be able to add items into their cart.
3. wishlist: users should be able to add items to their wishlist.
4. checkout + payment
5. view orders: users should be able to track their active orders

## Non functional requirements

Imagine there is a sale on an item with 50% off. And everybody is
waiting for 12:00 so they can order. In such situation, we do not want 2
users to order the same product. Hence, the system should be highly
consistent.  Secondly, the system should have low latency, therefore,
they can book as fast as possiblem, when there is a sale. 

The system should be highly available. You almost never want to show
"Something went wrong" errors or at least reduce them as much as
possible.

But given the constrains of CAP theorom, we cannot achieve high
availability and consistency at the same time. Therefore, we need to
segregate endpoints as per availability and consistency requirements. 

For example, endpoints dealing with payments, inventory counting, order
booking need to be highly available.  On the contrary, endpoints dealing
with search, product showcase, etc. need to be highly available.

## How are products onboarded onto the system

For an e-commerce website like amazon/flipkart. They have thousands of
suppliers (i.e. vendors who list their products on the platform).

Now each of these third party vendors need to register their products
onto the system. Suppose, bob is a vendor and he just received a stock
of 100 laptops from the manufacturer. He aims to sell 30 of them through
our platform oneline.

Bob will send a POST request to an endpoint like `add-product`:

```
POST /register-product

{
    "name": "Lenovo Laptop",
    "description": "A gaming laptop with 10 GB ram and 256 GB SSD ...",
    "price_usd": 200,
    "remaining_supply": 30
    // other metadata
}
```

When this request enters inside of our VPC, it fires up an event in
Kafka, in a topic like "new-product". The subscribers to this event can
then take appropriate action. For example, we might want to add product
in the database, etc.

![](https://i.ibb.co/m5cLtJYm/Screenshot-from-2025-09-25-22-00-11.png)

## Database

How will me model an item. For example, a product item. We need to
consider that we are selling disparate items, for example a t-shirt item
will have size, color. In contrast, a phone will have RAM, camera pixel
as its attributes.

What we are dealing here is unstructured data. Therefore, we cannot use
a SQL database. Instead, we shall use something like mongoDB, or
cassandra which store data in semi-structured format.

## Searching

We are going to support, millions of items. Therefore, we need to index
these items such that they can be searched quickly. It is also necessary
to pre-compute results or store metadata about items. For more
information on such systems, see [design of a search engine
blog](https://abdulrahim.space/posts/design_of_youtube/).

In our e-commerce platform, we are going to use [elasti
search](https://www.elastic.co/elasticsearch). It is increadibly
versatile search solution which provides full text search capabilities.
We can run various algorithms like fuzzy search or vector embeddings
based search.

## Serviciability

An ecommerce platform depends on many intermediaries for delivering the
product to the customer. For example, we might need a courier service
like fedex to transport goods from one place to another.

Given a product and a user's location, we might not be able to deliver
the product to the user. May be the user is located in a remote location
where, we do not have coverage from any courier service. In that case we
need to let the user know that they cannot order the product in
question.

To answer questions like, "weather product X can be delivered to product
Y" we need to implement a servicability endpoint which:

Given the vendor location (location from where to pick up the product),
user location (location where the product needs to be delivered), type
of product (e.g. refigrated items, eatables with expiry etc.) and other
relevant metadata.  It determines weather the product can be delivered. 

It should also provide us with the time by which we can deliver the item
to the user. This will be useful in displaying something like "delivery
in 2 days" etc.

## Recommendations

Each time a user searches a particular item, or view a particular item,
order an item, etc. We can use these user interations to infer what the
user is looking for. For example, if the user searches for different
mobile phone models, views some of them, then the user probably is
looking to buy a mobile phone. And this information can be used by the
recommendation engine to show appropriate products on the user's home
feed.

The idea is, that all the events that can be an input to the
recommendation engine, we put them in Kafka. Now, there will be services
that will fetch these events from kafka, and feed them into machine
learning models, and generate predictions.

We can use [spark](https://spark.apache.org/) to user activity from
kafka, and then run analytics on it. We can use spark for both stream
processing and batch processing. It can run ML jobs in parallel.  Along
with spark, we can also use [hadoop](https://hadoop.apache.org/) in
conjunction. Hadoop provides the low level utilities like HDFS (a
distributed file system) that enable us to run a large number of
analytics jobs  in parallel.
