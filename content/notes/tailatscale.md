---
title: "Tail At Scale"
date: 2020-04-23T15:59:53+08:00
draft: false 
---

# Tail at Scale

## Problem

Temporary high latency episodes may come to dominate service performance at scale. These things occur at the end of the latency curve and are problematic. 


Hence, "Latency tail-tolerant" because we need to optimize for the "tail" of this latency. 

The techniques focus on allowing *high system utilization without wasteful overprovisioning*.


## Sources of variability

- Shared resources

- Daemons

- Global Resource Sharing

- Maintenence

**Compounded by the fact that many parallelization techniques require us to fan out and fan in**.

If there is a *p* probability that a server request timesout, the probability that n servers have at least 1 timeout is  1 - (1-p) ^ n.

If p = 0.01, and n = 100, there is a 0.63 chance that it times out. 

## Technique 1: Within request short-term

- *Hedged requests*: issue same request to multiple replicas and use the results for whichever returns first

- *Tied requests*: enqueue several copies of a request in multiple servers simultaneously and allowing the servers to communicate updates of the status of these copies to each other. When request begins execution, 'cancel' the other requests.

## Technique 2: Cross request long-term adaptations

- *Micro partitions*: More partitions than there are machines in the service. With dynamic assignment and load balancing. Helps with failure recovery.

- *Selective replication*:Identify partitions that are going to be used more and create more copies of it.

- *Latency induced probation*: Use latency to detect faulty machines.
