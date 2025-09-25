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
    "name": "KUHL Laptop",
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

How will me model the item data.
