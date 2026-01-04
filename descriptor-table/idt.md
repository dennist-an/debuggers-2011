# Interrupt Descriptor Table
The Interrupt Descriptor Table (IDT) is a data structure set up by the OS which defines which functions handle which hardware and software interrupts. 

## Printing the entire IDT

The entire IDT can be printed out with the `!idt` command.

```
0: kd> !idt

Dumping IDT: fffff8010b661000

00: fffff80107401600 nt!KiDivideErrorFault
01: fffff80107401940 nt!KiDebugTrapOrFault  Stack = 0xFFFFF8010B69F000
02: fffff80107401e40 nt!KiNmiInterrupt  Stack = 0xFFFFF8010B691000
03: fffff80107402300 nt!KiBreakpointTrap
04: fffff80107402640 nt!KiOverflowTrap
...
```

The SwishDbg plugin also has the !ms_idt command, which will print out the IDT. But it will also attempt to detect if a given interrupt is either patched (via an assembly level change within the handler), or hooked (via a IDT table-level change). This was commonly the case before Microsoft’s PatchGuard and Virtualization-Based Security (VBS) started protecting the IDT.

```
0: kd> !ms_idt
    |-----|-----|--------------------|--------------------------------------------------------|---------|--------|
    | Cre | Idx | Address            | Name                                                   | Patched | Hooked |
    |-----|-----|--------------------|--------------------------------------------------------|---------|--------|
    |   0 |   0 | 0xFFFFF8042D409B00 | nt!KiDivideErrorFault                                  |         |        |
    |   0 |   1 | 0xFFFFF8042D409E40 | nt!KiDebugTrapOrFault                                  |         |        |
    |   0 |   2 | 0xFFFFF8042D40A300 | nt!KiNmiInterrupt                                      |         |        |
    |   0 |   3 | 0xFFFFF8042D40A7C0 | nt!KiBreakpointTrap                                    |         |        |
    |   0 |   4 | 0xFFFFF8042D40AB00 | nt!KiOverflowTrap                                      |         |        |
    |   0 |   5 | 0xFFFFF8042D40AE40 | nt!KiBoundFault                                        |         |        |
...
```

## Printing a specific interrupt number

A specific interrupt number can be printed with

`idt [interrupt #]`

```
0: kd> !idt 0n14

Dumping IDT: fffff8010b661000

0e: fffff80107404a00 nt!KiPageFault
```