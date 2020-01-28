---
title: "Raft"
date: 2020-01-28T17:25:09+08:00
---

# Raft 

## Summary
A consensus algorithm that is usually used to keep replicated logs consistent. Created to be easier to understand (than Paxos).
Replicated logs can be used to implement replicated state machines, e.g. distribtued databases.
Since state machines are deterministic, feeding them the same instructions lead to the same output, hence keeping the log consistent is important.

Raft:
 - can tolerate non-byzantine failures e.g. partitions, fail-stop servers, lost/delayed messages
 - does not depend on timing
 - in general, works as long as a majority of servers are up

A raft cluster contains several servers, each server is a Follower, Candidate or Leader.
In normal operation, for a given term, there is only one leader. 

Raft defines two RPCS: AppendEntries (AE) and RequestVotes (RV), which are used for the following operations:

## Leader election
 - Leader establishes "leadership" for this term by periodically sending out heartbeats (empty AEs) to other nodes 
 - If a Follower doesn't receive a heartbeat for too long, it becomes a candidate, triggers election for term + 1. 
 - Candidate tries to gather votes from other nodes by RV: other nodes will respond yes if the candidate has a equal or higher term and have not yet voted for this term, and Candidate's log is up-to-date
 - If Candidate gathers majority, it becomes leader, and begins sending out hearbeats for term + 1. Else remain candidate.
 - If the Leader is partitioned, some other Follower will eventually transition to Candidate.
 - If a Follower(s) is partitioned, as along as a majority are up, they will be able to elect a Leader anyways.
 - How to depose old Leader/loser Candidates? They will resign and become Followers once they receive heartbeats from the new Leader.

All commands from clients are issued to the Leader, then replicated to followers. (Another property that makes Raft easier to understand: log entries flow in only one direction.)

## Log replication
 - Once a leader is elected, it services all client requests (leader-based approach). Commands from clients are recorded as logs in Leader, then replicated via AE to other nodes. 
 - Logs are committed (= safe to apply) once the leader in the same term has replicated it to a majority of servers 
 - Leader's commitIndex is known to other nodes via AE, so that they know when its safe to apply logs. The Leader also keeps track of which log the others have replicated up-to.
 - Log entries replicated via AE may be lost by Follower crashes, Leader crashes
 - Raft maintains Log Matching Property (refer to paper)
 - Consistency check in AE guarantees that the log preceeding the one to be appended matches. If they don't match, leader re-replicates old logs to server.
 - Inconsistent logs are resolved by forcing Followers to duplicate Leader's log (leader-based approach). Old logs might be overwritten by new Leaders (refer to tutorial)


## Potential issues
 - Heartbeat timeout too high, election timeout too low: will cause unnecessary leadership changes
 - Multiple elections with split votes (no majority). Election timeouts should be random to prevent this.
 - Where nodes are far apart, election timeouts must be large enough

## Comparision with Paxos
| Raft | Paxos |
| - | - | 
| Leader-based | Peer-to-peer communication (but a leader is usually chosen anyways) |
| Easy to understand | Hard-to-understand, implement |

## Questions
 - Paper says "Raft implements linearizable semantics" == Raft supports strongly consistent? 
 - Q: Performance differences with Paxos? (I got no numbers)
   - A: Not really once a leader is elected