---
title: "Fault-Tolerant Virtual Machines"
date: 2020-01-23T16:23:37+08:00
draft: false
---

# Why use VMs?
Insert some answer here

- What does it mean to have difficulties in ensuring deterministic
  execution of physical server (esp as frequencies increase?)

# Deterministic Replay implementation

-   Non-deterministic events, non-deterministic operations (virtual
    interrupts, clock cycle counters respectively)
    -   Events - remember which instruction at which they occur at
    -   Operations: "sufficient information" is kept about them - such
        as? remember what they're undefined for and checking for that
        condition? (same "state change" and output guaranteed)

# Protocol:

-   Entries are not written to disk, instead they are sent on a
    logging channel and played on the backup disk in real time.
-   Output rule ensures that backup is consistent with other outputs
    by the primary server
-   Compensation for lost packets (network failures?)

# Fault-tolerance/detection of failures & recovery mechanisms

-   VMWare FT automatically detects when the backup VM is now the
    primary VM, and broadcasts the IP of the new Primary VM so that
    clients may know who the right person to contact is now.
-   Atomic test-and-set operation on a shared disk? Incrementing
    number for test and set?
-   Need for backup VM to execute at roughly the same pace
    -   Reduce unexpected pauses caused by buffer full
    -   reduce execution lag time in order to reduce the failover time
    -   Solutions: feedback loop (by measuring execution time lags), and
        using that to feedback to Primary VM to slow down/speed up
        execution when needed

# Implementation difficulties

## Disk IO:

-   Nondeterminism in asynchronous disk reads/writes. **Solution:** VM
    intercepts kernel calls and ensure that they are performed
    synchronously (or sequentially)
-   Nondeterminism in disk/memory access. **Solution:** "bounce
    buffers": Disk reads are first read into buffer then forwarded to
    replay as I/O processes on backup VM. Likewise, writes are first
    buffered before performed, then passed to backup VM to replay.
-   VM Failure before finding out if I/O completion
    notice. **Solution:** Reissue command on restart of VM. Because of
    the previous guards, it should be the case that this still
    results in a sequential, idempotent history.

## Network IO:

1.  Asynchronous network calls were somehow eliminated in the
    hypervisor??
2.  Interrupts are batched by clustering them for a bunch of packets
    (???) - reduces VM traps and interrupts
3.  log entries and acknowledgements handled by VMWare hypervisor,
    such that no thread context switches are not required

# Alternatives to implementations:

## Non-shared disks

1.  This implies that the disk is no longer an externally observable
    output, and hence no longer required to meet the Output
    Requirement - disk writes need not be synchronised as Output
    Rule need not be met.
2.  Resync of disk content in this context needed
3.  Non-shared disk config: split-brain problem cannot be solved due
    to lack of atomic test-and-set to be performed - 3rd party
    server needed to break ties.

## Disk reads done on the Backup VM

1.  Reduces traffic on logging channel
2.  Increases execution lag as Disk I/O needs to be performed on the backup disk

