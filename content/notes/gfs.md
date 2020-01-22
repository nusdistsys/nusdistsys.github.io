---
title: "GFS"
date: 2020-01-22T12:04:06+08:00
draft: false
---
## Goals for GFS
1. High probability of component failures - requirement to
   detect and recover promptly from such failures.
2. Common case: a lot of *large* files, (small files also
   supported, but not optimised)
3. Support for large streaming reads as well as small random reads
4. Large appends the common use case (to be optimised for this)
5. Atomicity for concurrent appends, with minimal synchronisation overhead
6. High bandwidth over low latency

## Architecture
![GFS architecture](https://2.bp.blogspot.com/-C7Qcn2akF7E/U0zVjII34hI/AAAAAAAAAQY/7Cvy2OX9m9s/w1200-h630-p-k-no-nu/GFS+architecture.JPG)

## Chunk size
### Balancing between big and small chunk size
- Larger chunk size: Pros
  - lesser chunks -> lesser metadata overhead on master server
  - With a large working set of file locations, client would still be
    more able to comfortably cache all chunk information within local
    memory
- Cons
  - Space wastage due to internal fragmentation (mitigated with lazy allocation)
  - If a small file has little number of chunks, accessing the file
    would cause the chunkservers to become hotspots (check paper for
    mitigation)

## Guarantees
![definitions](https://i.imgur.com/MCJQPkq.png)

## How it works
- The paper discusses this better than I ever can

## Other interesting properties/mechanisms
### Namespace management and locking
- No per-directory data structure
- When a new file is created:
  - Read locks acquired on all parent directories
  - Write lock is acquired on the new file
- Implication:
  - Multiple clients can make new files on same directories
  - Read lock ensures that directory is not deleted when new file is
    being created

### Garbage collection and stale replication detection
- Version numbers are maintained and reported periodically to master
  server by the chunk servers
- Checked again when a lease is about to be given to a chunkserver

### Checksums
- Computed on each chunkserver - why?
- "Corruption" - referring to data being corrupted locally due to
  equipment failure, as opposed to transport corruption.
