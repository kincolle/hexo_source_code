---
title: PubSub
date: 2018-05-09 23:13:48
tags:
categories:
- Redis
- PubSub
---

## Introduction of PubSub
Here is the introduction from [redis.io](https://redis.io/topics/pubsub)

SUBSCRIBE, UNSUBSCRIBE and PUBLISH implement the Publish/Subscribe messaging paradigm where (citing Wikipedia) senders (publishers) are not programmed to send their messages to specific receivers (subscribers). Rather, published messages are characterized into channels, without knowledge of what (if any) subscribers there may be. Subscribers express interest in one or more channels, and only receive messages that are of interest, without knowledge of what (if any) publishers there are. This decoupling of publishers and subscribers can allow for greater scalability and a more dynamic network topology.

![](Redis-PubSub/2.png)

## Implementation
#### SUBSCRIBE 
![](Redis-PubSub/3.png)

When a client subscirbes a channel, redis need to bind the channel and the client together.
#### PSUBSCRIBE 
![](Redis-PubSub/4.png)
When a client want to get messages from a channel model, redis need to bind the channel and the client together too.

#### PUBLISH
![](Redis-PubSub/5.png) 
Send messages to clients

#### Architecture
![](Redis-PubSub/6.png)

