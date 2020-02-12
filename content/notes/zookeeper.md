---
title: "Zookeeper"
date: 2020-02-12T15:12:27+08:00
draft: false
---
# What is Zookeeper?
Zookeper is a distributed data store. It can be used for configuration management, Rendezvous, or for implementing group membership.

## Zab Protocol

- This wasn't discussed in detail in the paper but one can refer to [this website](https://distributedalgorithm.wordpress.com/2015/06/20/architecture-of-zab-zookeeper-atomic-broadcast-protocol/]) for a more detailed overview


## What kind of nodes can a client create?
- Ephemeral znodes
- Regular znode

When adding a znode, a client can set a sequential flag.

Zookeeper implements watches to allow clients to receive timely notifications of changes without requiring polling

## What does zookeper guarantee?
- (Asycnchronously) Linearizable writes : All requests that update the state of Zookeeper are serializable and respect precedence.
- FIFO client order: All requests from a given client are executed in the order that they were sent by the client 