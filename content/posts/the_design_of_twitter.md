---
date: 2025-09-24T06:55:19.889Z
layout: post
title: "The design of twitter"
subtitle: In this blog post, I want to talk about the design of twitter. Particularly, how timelines are generated...
description: In this blog post, I try to decode how your tweets are agreegated, and how twitter does it for millions of users in a scalable way

category: ["system design"]
tags: ["software engineering", "design"]
author: abdulrahim
---


## Introduction

Firstly I would like to talk about the scale of twitter, which will help
us to understand the requirements of a system like twitter.

Twitter handles about 600 tweets per second. Which means that
approximately 600 tweets are made each second. On the contrary,
approximately 60,000 tweets are read each second. This clearly shows
that twitter is a read heavy system.

There are approximately 300 million users of twitter.  Some of them are
active users (users who frequently visit twitter), while most of them
are passive users (i.e. users who'd only visit once in a while, say 1
month).

## Features (i.e. Functional requirements)

1. Home timeline

    - every user has a home timeline
    - a home timeline contains tweets from the users, you've followed
      (i.e. your followees), or tweets that are recommended by the
      twitter recommendation engine

For the sake of simplicity, we will not include the tweets from
recommendation engine in the home timeline. Therefore, the home timeline
shall only include the tweets from your followees.

2. User timeline

    - each user has a user timeline
    - user timeline shows the tweets made by you


3. Search timeline

    - suppose you search "world cup" on twitter search, the search
      results will show you a number of tweets related to "world cup"
    - these tweets are called search timeline

## Non functional requirements

It is clear from the description that we need to optimize our design for
the following features:

- scalability: It is clear that you need a scalale system that needs to
  handle 300 million users
- availability: If the user is trying to see a tweet. He should be able
  to see it. You typically do not want to show "Something went wrong"
  error screens, which will make it a poor user experience
- eventual consistency: If a tweet is delivered to your followers with a
  few second delay, it should not be a huge problem (typically)
- causal consistency: If I make a new tweet, it should be shown to me
  immediately, or I might make a duplicate new tweet thinking that the
  previously created tweet was not successfully posted

## Low level design

Considering the non-functional requirements. It should be clear to us,
that we would require a No-SQL database, particularly mongo-db or
cassandra.

This is because, No-SQL databases are easier to scale, and both mongodb
and cassandra are build to favour availability over consistency.

I wanted to talk about database schema but I would like to emphasize
that NoSQL databases do not have a schema in the same sense as a
relational database (SQL based) does.

Therefore, lets rather talk about API endpoints (request/response
shapes), since they are database agnostic.


1. User

We should be able to fetch a user by a user id or email. A typical
response would look like:

```json
{
    "user_id": 1231231,
    "email": "sample@example.com",
    "name": "sample",
    "phone": "+12 123124214",
    "created_at": 2143124214124,
    // other metadata
}
```

3. Tweet

Each tweet, has a limit of 140 characters. And each tweet can contain
images/videos. The basic structure defining a tweet object might look
like the following:

```json
{
    "tweet_id": 1231232,
    "text": "the text of the tweet is stored in this field",
    "images": [
        "https://twitter.com/user-data/images/<image_id>",
        "https://twitter.com/user-data/images/<image_id>"
        // a list of paths to images in this post.
    ],
    "videos": [
        "https://twitter.com/user-data/videos/<video_id>",
        "https://twitter.com/user-data/videos/<video_id>"
        // a list of paths to videos in this tweet.
    ]
}
```

2. User timeline

We should be able to retrieve all the posts, made by a particular user.
Since, there may be a large number of tweets/posts, we can also
implement pagination functionality. A sample REST API response might
look like:

Request:

```json
/user-timeline POST

{
    "user_id": 123123,
    // filter conditions
}
```

Response:

```json
{
    "123123": {
        "text": "some text of tweet 1",
        "images": [
            // list of paths to images
        ],
        "videos": [
            // list of paths to videos
        ]
    },
    "321321": {
        "text": "some text of tweet 2",
        "images": [
            // list of paths to images
        ],
        "videos": [
            // list of paths to videos
        ]
    },

    // and many more tweets in the form
    // "tweet_id": { Tweet Object }
}
```


