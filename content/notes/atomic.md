---
title: "Atomic"
date: 2020-04-14T12:18:54+08:00
draft: false
---

# Atomic

```
Goal: define atomicity and describe strategies on how it can be achieved
```

Property required in several different areas of computer system design.


There are two main kinds of atomicity, but different sources of literature might define these things differently.

`All-or-nothing` is regarded as the default definition of atomicity.

`Before-or-after` is sometimes regarded as *Isolation*.


There remain some important gaps in our exploration of atomicity. First, in a **layered system**, a transaction implemented in one layer may consist of a series of component actions of a lower layer that are themselves atomic. The question is how the commitment of the lower-layer transactions should relate to the commitment of the higher layer trans­ action. If the higher-layer transaction decides to abort, the question is what to do about lower-layer transactions that may have already committed. There are two possibilities:
• Reverse the effect of any committed lower-layer transactions with an UNDO action. This technique requires that the results of the lower-layer transactions be visible only within the higher-layer transaction.
• Somehow delay commitment of the lower-layer transactions and arrange that they actually commit at the same time that the higher-layer transaction commits.




## All-or-nothing Atomicity

```
A sequence of steps is an all-or-nothing action if, from the point of view of its invoker, the sequence always either
- completes, or
- aborts in such a way that it appears that the sequence had never been undertaken in the first place. That is, it backs out.
```

### Techniques:
This layer implements what is known as *journal storage*. The basic idea of journal storage is straightforward: we associate with every named variable not a single cell, but a list of cells in non-volatile storage; the values in the list represent the history of the variable.

## Before-or-after Atomicity


```
Concurrent actions have the before-or-after property if their effect from the point of view of their invokers is the same as if the actions occurred either completely before or completely after one another.
```

Several actions that concurrently operate on the same data should not interfere with one another. 

### Techniques:

1. System-wide lock

Anytime any shared resource is needed, acquire a 


2. Fine-grained locks
needed locks at begin_transaction and release all of them at end_transaction

  - advantage: easy to reason about. works great if it could be implemented

  - disadvantage: requires transaction to know all of its needed locks in advance

3. Two phase locking


Gradually acquire locks as you need them (phase 1). Primary constraint is that the transaction may not release any locks until it passes its lock point.(phase 2) Further, the transaction can release a lock on an object that it only reads any time after it reaches its lock point if it will never need to read that object again, even to abort.

The name of the discipline comes about because the number of locks acquired by a transaction monotonically increases up to the lock point (the first phase), after which it monotonically decreases (the second phase). 

Informally, once a transaction has acquired a lock on a data object, the value of that object is the same as it will be when the transaction reaches its lock point, so reading that value now must yield the same result as waiting till then to read it. Furthermore, releasing a lock on an object that it hasn’t modified must be harm­less if this transaction will never look at the object again, even to abort.



## Distributed Two-phase commits

TLDR:

Distributed version of a two phase commit protocol. Difference is that coordinator and workers cannot reliably communicate, thus reduces to constructing a reliable distributed version of two-phase commit protocol using persistent senders and duplicate suppression.
