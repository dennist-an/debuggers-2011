# Global Descriptor Table

The Global Descriptor Table (GDT) is a data structure set up by the OS which defines regions of code and data, including the “ring” privilege levels at which they can be used. There is no native WinDbg command to print the GDT. Therefore the previously mentioned SwishDbg extension was modified to provide more verbose GDT output.

The below is an example output for a 2-core system, because there are separate GDT Registers per core, which can point at different GDTs.

```
0: kd> !ms_gdt
    |-----|-----|---------|-----|-----------|--------------------------|--------------------|
    | Cre | Idx | Present | DPL | RawS+Type | Type (interpreted)       | Base               | 
    |-----|-----|---------|-----|-----------|--------------------------|--------------------|
    |   0 |   0 |       0 |   0 |         0 | Not present, not valid   | 0x0000000000000000 |
    |   0 |   1 |       0 |   0 |         0 | Not present, not valid   | 0x0000000000000000 |
    |   0 |   2 |       1 |   0 |        1b | Code RE Ac               | 0x0000000000000000 |
    |   0 |   3 |       1 |   0 |        13 | Data RW Ac               | 0x0000000000000000 |
    |   0 |   4 |       1 |   3 |        1b | Code RE Ac               | 0x0000000000000000 |
    |   0 |   5 |       1 |   3 |        13 | Data RW Ac               | 0x0000000000000000 |
    |   0 |   6 |       1 |   3 |        1b | Code RE Ac               | 0x0000000000000000 |
    |   0 |   7 |       0 |   0 |         0 | Not present, not valid   | 0x0000000000000000 |
    |   0 |   8 |       1 |   0 |         b | TSS32/64 (Busy)          | 0xFFFFF8010B662000 |
    |   1 |   0 |       0 |   0 |         0 | Not present, not valid   | 0x0000000000000000 |
    |   1 |   1 |       0 |   0 |         0 | Not present, not valid   | 0x0000000000000000 |
    |   1 |   2 |       1 |   0 |        1b | Code RE Ac               | 0x0000000000000000 |
    |   1 |   3 |       1 |   0 |        13 | Data RW Ac               | 0x0000000000000000 |
    |   1 |   4 |       1 |   3 |        1b | Code RE Ac               | 0x0000000000000000 |
    |   1 |   5 |       1 |   3 |        13 | Data RW Ac               | 0x0000000000000000 |
    |   1 |   6 |       1 |   3 |        1b | Code RE Ac               | 0x0000000000000000 |
    |   1 |   7 |       0 |   0 |         0 | Not present, not valid   | 0x0000000000000000 |
    |   1 |   8 |       1 |   0 |         b | TSS32/64 (Busy)          | 0xFFFFFFFFFFBB7000 |
```