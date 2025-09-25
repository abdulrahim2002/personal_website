---
date: 2025-09-25T07:01:18.924Z
layout: post
title: "The design of a Search Engine"

subtitle: Google indexes millions of web pages, and collates the search results over milliseconds while searching all these millions of web pages...
description: In this article, lets take a deep dive into how a system
like google search actually works

category: ["system design"]
tags: ["software engineering", "design"]
author: abdulrahim
---

## Introduction

First let's start with a similar problem akin to web search. Imagine you
have 100 files in your current directory, and you do something like:

```
grep "Abdul" .
```

The grep program will return you a list of files which contain the given
string or pattern. But how do you think does that works under the hood.

In a naive approach, the grep program would open each file in the
directory, search for the given string/pattern and if any result is
obtained, then add this file to the output list.

Typically, this is how the result would look like:

```bash
{25 Sep 12:43}~/Desktop/open-pocket-backend-server/src:main ✓ ➭ grep -r "interface"
commons/IJwtPayload.ts:export default interface JwtPayload {
commons/fastifyPassport.ts:    interface PassportUser extends IUserShape {}
commons/IDbControllerResponse.ts:interface IDbControllerResponse<T=any> {
commons/houndError.ts: * interface.
api/v1/actions/delete.action.ts:interface IDeleteAction {
api/v1/actions/archive.action.ts:interface IArchiveAction {
api/v1/actions/tag_delete.action.ts:interface ITagDelete {
api/v1/actions/tags_clear.action.ts:interface ITagsClear {
api/v1/actions/tags_replace.action.ts:interface ITagsReplace {
api/v1/actions/favorite.action.ts:interface IFavoriteAction {
api/v1/actions/tags_add.action.ts:interface ITagsAddAction {
api/v1/actions/unfavorite.action.ts:interface IUnfavoriteAction {
api/v1/actions/add.action.ts:interface IaddActionParams {
api/v1/actions/tags_remove.ts:interface ITagsRemove {
api/v1/actions/readd.action.ts:interface IReaddAction {
api/v1/actions/tag_rename.action.ts:interface ITagRename {
configs/main.config.ts:// define the interface/contract for mainConfig
configs/main.config.ts:interface IMainConfig {
```

Now how does this problem relate to search engines?

- Search engines operate in a similar fasion. Instead of files in a file
  system, imagine your have html pages instead.
- Now users come to you with a search string, for example "best
  resteraunt in pune".
- Now you need to search for all the html pages in your system, and find
  the pages that are appropriate to the search query. There are multiple
  ways of doing this. From as simple as runnin a pattern matching
  algorithm to find the exact string, to running advanced NLP pipelines.
- Lastly, you just return the list of found pages, relevant to the
  query. 

And this is the whole idea behind a search engine. But the currrent
approach i.e. to just search all the web pages on the internet on
receiving a request is not scalable.

Google entertains about 150k search requests per second. And for each
request, they are able to return the results within a few hundreds
milliseconds at maximum.

To enable such a massive scale, google heavily relies on
pre-computation. In fact, almost nothing is computed on the fly, all the
results need to be pre-fetched and cached.

## Google crawler

Google has a massive web crawler that is responsible for crawling the
internet and find web pages.

