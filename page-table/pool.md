# Pool

The Windows kernel has multiple “pools”, which are really just heaps from which memory can be allocated. Pools can have different "tags", to help developers identify which allocations came from their own code (if they specified a custom tag at allocation time), or when the data is from a pool of all the same type of data structures.

Sometimes when analyzing Windows itself, the !pool command can be useful to sanity check if a random memory address happens to be associated with a given pool type which is used exclusively for a given structure type.

## More Explanation

Think of a pool as a dedicated reservoir of memory. Unlike regular apps that request memory from the system, the kernel manages its own space to ensure high performance and stability. There are two primary types of pools:

    Nonpaged Pool: Memory that must stay in physical RAM at all times (used for critical tasks that can't wait for a hard drive to spin up).

    Paged Pool: Memory that can be "paged" out to the hard drive if the RAM gets too full.

### What are "Tags"?

A Pool Tag is a 4-character identifier (like File, Ntfs, or Thre) attached to a memory allocation.

    For Developers: If a driver is leaking memory, they can look at the tags to see which specific part of the code is "hogging" the pool.

    For System Health: Tools like PoolMon (Pool Monitor) allow administrators to see these tags in real-time to diagnose why a computer is slowing down or crashing.

### Why does this matter?

If the kernel runs out of "Pool" memory, you get the infamous Blue Screen of Death (BSOD). By using different pools and tags, Windows prevents one buggy driver from accidentally overwriting the memory used by a different, healthy part of the system.

## Technical 
E.g. note below how when taking an _EPROCESS structure address, the !pool command says “Pooltag Proc : Process objects”, indicating that the address does indeed belong to a pool with the tag ‘Proc’, which is used for process objects.

```0: kd> !process -1 0
PROCESS ffffdc0155e94340
    SessionId: 0  Cid: 0d94    Peb: 1585fea000  ParentCid: 029c
    DirBase: 125bbe000  ObjectTable: ffff81011ceac140  HandleCount: 1132.
    Image: MsMpEng.exe

0: kd> !pool ffffdc0155e94340
Pool page ffffdc0155e94340 region is Nonpaged pool
 ffffdc0155e94000 size:  250 previous size:    0  (Allocated)  MmCi
 ffffdc0155e94250 size:   60 previous size:    0  (Free)       7+.#
*ffffdc0155e942c0 size:  d00 previous size:    0  (Allocated) *Proc
        Pooltag Proc : Process objects, Binary : nt!ps
 ffffdc0155e94fc0 size:   20 previous size:    0  (Free)       .+.#```