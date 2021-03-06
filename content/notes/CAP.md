---
title: "CAP"
date: 2020-04-02T15:22:02+08:00
draft: false
---

Tip: Usually analysed in the context of a master with its
replicas. Consider reads and writes sent to the replicas.

## Theorem

### Consistency

Every read returns the most recent right, or an error.

#### Eventual Consistency

- Low latency but stale data

#### Strong Consistency

- High latency but latest write


### Availability

Every requests receives a non-error response, without the guarantee
that it contains the most recent write.

### Partition Tolerance

System operates despite aribitrary number of messages droppped by network.
