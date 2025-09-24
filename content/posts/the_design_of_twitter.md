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
    "followees": [
        123, 321, 321,
        // a list of users, current user follows
    ],
    // other metadata
    "phone": "+12 123124214",
    "created_at": 2143124214124,
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
        "tweet_id": 123123,
        "text": "some text of tweet 1",
        "images": [
            // list of paths to images
        ],
        "videos": [
            // list of paths to videos
        ]
    },
    "321321": {
        "tweet_id": 321321,
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

## How to generate home page timeline/feed

Suppose we need to generate the feed for alice. Support alice follows
bob and charlie. Therefore, in our simplified twitter, alice should see
more recent (say 20) posts from bob and charlie.

![Alice follows bob and charlie](https://i.ibb.co/5WvZWw27/Screenshot-from-2025-09-24-14-51-23.png)

When we need to generate the home feed for alice, the simplest option is
to use the following algorithm:

1. Find the friends of `alice`, using the "followees" field. This will
   provide us user id's of `bob` and `charlie`
2. Now get all the tweets from `bob` and `charlie` using their user
   timelines respectively (this will involve a database call)
3. Sort the agreegated tweets from bob and charlie and return top 20
   tweets.

In fact, I previously implemented a [similar problem on
leetcode](https://leetcode.com/problems/design-twitter/descript). And
this was the solution I came up with (which follows the above algorithm):

```
interface Post {
    // each post has an id and timestamp
    id: number,
    sno: number,
}

interface User {
    // store ID's of users this user follows
    followees: Set<number>,
    // store tweetID's of tweets made by user
    posts: Post[],
}

class Twitter {
    // to be able to get User object from UserID
    private usersList: Map<number, User> = new Map();
    private postSerialNumber: number = 0;

    postTweet(userId: number, tweetId: number): void {
        if ( !this.usersList.has( userId ) )
            this.createUser( userId );
        
        const curUser: User = this.usersList.get( userId );
        
        const tweet: Post = {
            id: tweetId,
            sno: this.postSerialNumber++,
        }

        curUser.posts.push( tweet );
    }

    getNewsFeed(userId: number): number[] {
        if ( !this.usersList.has( userId ) ) 
            return [];

        const feed: Post[] = [];
        
        const curUser: User = this.usersList.get( userId );

        curUser.followees.forEach( (followeeId) => {
            // the the posts of this followee and add that to feed
            const followee: User = this.usersList.get( followeeId );
            feed.push( ...followee.posts );
        } );

        // sort the posts by serial no. and return the top 10 posts
        feed.sort( (a,b) => b.sno - a.sno );
        return feed.slice( 0, 10 ).map( x => x.id );
    }

    follow(followerId: number, followeeId: number): void {
        if ( !this.usersList.has(followerId) )
            this.createUser( followerId );

        if ( !this.usersList.has(followeeId) )
            this.createUser( followeeId );

        const curUser: User = this.usersList.get( followerId );
        curUser.followees.add( followeeId );
    }

    unfollow(followerId: number, followeeId: number): void {
        const curUser: User = this.usersList.get( followerId );
        curUser.followees.delete( followeeId );
    }

    createUser( userId: number ) {
        const newUser: User = {
            followees: new Set( [userId] ),
            posts: [],
        }
        this.usersList.set( userId, newUser );
    }
}
```

The problem with this approach is that:

1. this is an expensive operation. For a user with 30 followees, we are
   making 30 expensive database calls. And then we also need to sort the
   tweets.
2. it generates the home feed on the fly (expensive operation -> will
   make user wait)


To improve the performance of the above approach, the idea is to keep
each user's timeline (both home timeline and user tiemline) in a redis. 

![All home/user timelines/feeds are redis
instances](https://i.ibb.co/93VmrnYB/Screenshot-from-2025-09-24-15-18-45.png)

Whenever, bob or charlie post a new tweet. A **fanout** service picks up
the new tweet and populates it in alice's home feed.

![When bob posts a tweet. Fanout service automatically populates alice's
feed with
it](https://i.ibb.co/mFvWB658/Screenshot-from-2025-09-24-15-23-32.png)

## How to generate search timeline/feed

Twitter uses something called inverted full text search. Here's how it
works:


When you make a tweet. Twitter does the following:


- identify the terms within your tweet, that can be used to index your
  tweet. For example, I you tweet:

  ```
    I am supporing manchester united this season.
  ```

- Then twitter parser may identify "manchester united" as the main
  search key for this tweet. Note that there may be multiple search
  keys.

- Then it stores the `search_key : tweet` mapping, into a key value
  store.

  For each `search_key` there is a list of tweets that contain that
  search key. Similarly, your tweet will be appended to the list of
  tweets, under the search key "manchester united"

```
// key value store
"machester united": [123, 321, 422, ... /* other tweet id's that talk about manchester united*/ ]
// other search_keys along with thir tweets
```

Now whenever, a user searches for "manchester united". Twitter can just
fetch the list of tweets from the above key value store. And your tweet
will be displayed in the results (based on some raking)

You might have already observed, that such a key-value store needs to be
of massive scale considering we are dealing with 300 million users. And
that's why, this key-value store is not located in a single place. This
is probably spread across multiple nodes and data centres.

Therefore, twitter uses an approch of scatter and gather when writing
and reading from this distributed key-value store.

For a given query, each datacenter is asked to provide a list of
relavant tweets from it's databases. These are merged together and
finally shown to the user after being ranked.

