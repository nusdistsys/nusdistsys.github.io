---
title: "Spark"
date: 2020-04-03T10:39:06+08:00
draft: true
---

# Spark


## Motivation

*Resilient Distributed Databases* is a distributed memory abstraction that programmers use to do in-memory computation in a fault-tolerant manner. 

Motivated by *iterative* algorithms and *interactive data-mining tools*. (These algorithms require **intermediate** results). RDDs provide restricted form of shared memory. 

## How does it solve the problem

RDDs provide a *coarse-grained* transformation. The system logs these transformations instead to build the dataset instead of saving the actual data. 

This allows recovered of a partition without using *replication*.



## More on fault tolerance guarantees

The main challenge in designing RDDs is defining a programming interface that can provide fault tolerance efficiently. Existing abstractions for in-memory storage on clusters, such as distributed shared memory [24], keyvalue stores [25], databases, and Piccolo [27], offer an interface based on fine-grained updates to mutable state (e.g., cells in a table). With this interface, the only ways to provide fault tolerance are to replicate the data across machines or to log updates across machines. Both approaches are expensive for data-intensive workloads, as they require copying large amounts of data over the cluster network, whose bandwidth is far lower than that of RAM, and they incur substantial storage overhead.

In contrast to these systems, RDDs provide an interface based on coarse-grained transformations (e.g., map, filter and join) that apply the same operation to many data items. This allows them to efficiently provide fault tolerance by logging the transformations used to build a dataset (its lineage) rather than the actual data.1 If a partition of an RDD is lost, the RDD has enough information about how it was derived from other RDDs to recompute just that partition. Thus, lost data can be recovered, often quite quickly, without requiring costly replication.

Although an interface based on coarse-grained transformations may at first seem limited, RDDs are a good fit for many parallel applications, because these applications naturally apply the same operation to multiple data items.


## Programming Interface

### RDD Operations

- *Transformations*: Lazy operations that define a new RDD

- *Actions*: Launch a computation to return value to program/write to external storage.


There exists both narrow dependencies and wide dependencies.

- Narrow dependecies: allow pipelined execution on one cluster node

- Wide dependencies: Wait for parent partitions to be completed before shuffling across everything in a map-reduce like manner

## Implementation

### Job scheduling

- Lineage graph forms a DAG, DAG contains pipelined transformations with narrow dependencies.

- Tasks are assigned based on data locality

- For shuffle dependencies, we materialize intermediate records. 

- If task processes a partition that is available in memory on a node, we send it to that node.

