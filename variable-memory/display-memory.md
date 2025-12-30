## Displaying Local Variables

When you are source-level debugging, you can use the “Display Local Variables” (dv) command, or you can open the Local Variables window as shown in the below video. Note however, that if you have not yet stepped through the function prolog, and into the function body, the debugger will display incorrect values for variables.

```
Breakpoint 0 hit
KmdfHelloWorld!DriverEntry:
fffff804`600b1000 4889542410      mov     qword ptr [rsp+10h],rdx
1: kd> dv
   DriverObject = 0xfffff804`5875cecc Driver {...}
   RegistryPath = 0xffffb38b`269ff000 "\REGISTRY\MACHINE\SYSTEM\ControlSet001\Services\KmdfHelloWorld"
         config = struct _WDF_DRIVER_CONFIG
         status = 0n5
1: kd> t
KmdfHelloWorld!DriverEntry+0xe:
fffff804`600b100e c744243000000000 mov     dword ptr [rsp+30h],0
1: kd> dv
   DriverObject = 0xffffb38b`26375e30 Driver "\Driver\KmdfHelloWorld"
   RegistryPath = 0xffffb38b`269ff000 "\REGISTRY\MACHINE\SYSTEM\ControlSet001\Services\KmdfHelloWorld"
         config = struct _WDF_DRIVER_CONFIG
         status = 0n535508544
```

Note how the DriverObject local variable address changed, and once changed, the driver name looks correct. The `t` command executes a single instruction or a single line of source code.

## Displaying Structures

The “Display Type” (dt) command can both display the fields of a structure, and interpret the memory at a given address according to a given structure type.
Display Structure Definition

To only display the top level fields of a structure of a given type:

dt {structure type}

```
1: kd> dt nt!_EPROCESS
   +0x000 Pcb              : _KPROCESS
   +0x438 ProcessLock      : _EX_PUSH_LOCK
   +0x440 UniqueProcessId  : Ptr64 Void
   +0x448 ActiveProcessLinks : _LIST_ENTRY
   +0x458 RundownProtect   : _EX_RUNDOWN_REF
   +0x460 Flags2           : Uint4B
   +0x460 JobNotReallyActive : Pos 0, 1 Bit
   +0x460 AccountingFolded : Pos 1, 1 Bit
   +0x460 NewProcessReported : Pos 2, 1 Bit
   +0x460 ExitProcessReported : Pos 3, 1 Bit
   +0x460 ReportCommitChanges : Pos 4, 1 Bit
   +0x460 LastReportMemory : Pos 5, 1 Bit
   +0x460 ForceWakeCharge  : Pos 6, 1 Bit
   +0x460 CrossSessionCreate : Pos 7, 1 Bit
   +0x460 NeedsHandleRundown : Pos 8, 1 Bit
   ...
```

Breakdown of the Command

    dt (Display Type): The command used to view symbols, structures, and their current values in memory.

    nt!: This tells WinDbg to look for the symbol in the Windows Kernel module (ntoskrnl.exe).

    _EPROCESS: The specific name of the C-style structure (prefixed with an underscore) defined in the kernel.


## Recursively Display Structure Definitions

To recursively display the fields of a structure (and sub-structures):

dt -r{depth} {structure type}

```
0: kd> dt -r2 nt!_EPROCESS
   +0x000 Pcb              : _KPROCESS
      +0x000 Header           : _DISPATCHER_HEADER
         +0x000 Lock             : Int4B
         +0x000 LockNV           : Int4B
         +0x000 Type             : UChar
...
```

## Display Memory as Structure

To interpret the memory at a given address according to a structure definition:

dt {structure type} {address}

```
0: kd> dt nt!_EPROCESS ffffb38b26516340
   +0x000 Pcb              : _KPROCESS
   +0x438 ProcessLock      : _EX_PUSH_LOCK
   +0x440 UniqueProcessId  : 0x00000000`00001858 Void
   +0x448 ActiveProcessLinks : _LIST_ENTRY [ 0xffffb38b`25aab748 - 0xffffb38b`263da4c8 ]
   +0x458 RundownProtect   : _EX_RUNDOWN_REF
   +0x460 Flags2           : 0x200d080
...
```
Note: it will interpret the given address as the given structure type, whether it makes sense or not. It’s incumbant on the analyst to apply the correct type to the correct address, based on other hints they may have obtained from the code (e.g. knowing that a specific parameter to a function is required to be of a specific type.)