# Examining Virtual Memory and Page Tables

Page tables are a mechanism by which a hardware memory management unit (MMU) translates from virtual memory addresses to physical memory addresses in RAM. Page tables are set up by software, and used automatically for translation by the MMU when the processor attempts to read or write data/code.

## Enumerating page table entries

The `!pte` command shows all the page table entries that would be encountered by the MMU as it attempts to translate a given virtual memory address to a physical address.

```
0: kd> !pte fffff80107401940
                                           VA fffff80107401940
PXE at FFFF944A25128F80    PPE at FFFF944A251F0020    PDE at FFFF944A3E0041D0    PTE at FFFF947C0083A008
contains 0000000004B09063  contains 0000000004B0A063  contains 0A00000002C001A1  contains 0000000000000000
pfn 4b09      ---DA--KWEV  pfn 4b0a      ---DA--KWEV  pfn 2c00      -GL-A--KREV  LARGE PAGE pfn 2c01     
```

## Translation with alternative page tables

!pte always translates an address by using the current page tables. If you’d like to use alternative page tables, you can either:

    Change process context to the other process, via !process and .process as previously mentioned, and then use !pte
    Use !process to find the “DirBase” (first page table) of the other process, and use that as the first parameter to !vtop

You can translate Virtual to Physical addresses with !vtop. It has two forms:

1. Set the context: .process /p ffffbd8e61b4d300
2. Translate: !vtop 0 0000007ecfb0a000

```
0: kd> !process 0 0
...
PROCESS ffffbd8e61b4d300
    SessionId: 1  Cid: 1b44    Peb: 7ecfb0a000  ParentCid: 06c0
    DirBase: 3a5d4000  ObjectTable: ffffa90d8f1aae40  HandleCount: 239.
    Image: notepad.exe
...
0: kd> .process /p ffffbd8e61b4d300
Implicit process is now ffffbd8e`61b4d300
.cache forcedecodeuser done
0: kd> !vtop 0 0000007ecfb0a000
Amd64VtoP: Virt 0000007ecfb0a000, pagedir 000000003a5d4000
Amd64VtoP: PML4E 000000003a5d4000
Amd64VtoP: PDPE 000000001af6efd8
Amd64VtoP: PDE 0000000046cef3e8
Amd64VtoP: PTE 000000004ef70850
Amd64VtoP: Mapped phys 000000000aaf1000
Virtual address 7ecfb0a000 translates to physical address aaf1000.
...
1: kd> !process 0 0 explorer.exe
PROCESS ffffb38b253e6340
    SessionId: 1  Cid: 10c0    Peb: 00ac1000  ParentCid: 1084
    DirBase: 12f3d4000  ObjectTable: ffff8b0cf7685140  HandleCount: 3179.
    Image: explorer.exe
1: kd> !vtop 12f3d4000 000000d2af11a000
Amd64VtoP: Virt 000000d2af11a000, pagedir 000000012f3d4000
Amd64VtoP: PML4E 000000012f3d4008
Amd64VtoP: zero PML4E
Virtual address d2af11a000 translation fails, error 0xD0000147.
```

The above example shows how virtual memory address 0000007ecfb0a000 translates to physical memory address 0x00000000aaf1000 in notepad.exe (through 3 equivalent command variants), but has no valid translation if attempted through the page tables for the memory space of explorer.exe.

## Listing all virtual memory addresses in a given process

The !ptov command shows all the Physical to Virtual translations for a given page table. The command takes the physical address of the starting page table (e.g. Page Map Level 4 for 64 bit paging), and prints out two columns. The first column is the physical address, and the second column is the virtual address it maps to. The list is ordered by ascending virtual addresses.

```
0: kd> !ptov 000000000aaf1000
Amd64PtoV: pagedir 000000000aaf1000
0000ffffc0000000 0000158880000000 (1GB large page)
0000ffffc0000000 0000159200000000 (1GB large page)
0000ffffc0000000 0000159b80000000 (1GB large page)
0000ffffc0000000 000015a500000000 (1GB large page)
0000ffffc0000000 000015ae80000000 (1GB large page)
0000ffffc0000000 000015b800000000 (1GB large page)
0000ffffc0000000 000015c180000000 (1GB large page)
0000ffffc0000000 000015cb00000000 (1GB large page)
0000ffffc0000000 000015d480000000 (1GB large page)
0000ffffc0000000 000015de00000000 (1GB large page)
0000ffffc0000000 000015e780000000 (1GB large page)
0000ffffc0000000 000015f100000000 (1GB large page)
0000ffffc0000000 000015fa80000000 (1GB large page)
0000000000000000 00001e8500000000 (1GB large page)
0000ffffffe00000 00001e9300e00000 (large page)
0000ffffffe00000 00001e9301000000 (large page)
00000000c0000000 00001e9301208000
0000ffffffe00000 00001e9301400000 (large page)

```