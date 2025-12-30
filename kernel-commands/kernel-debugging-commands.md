# Kernel Debugging Commands

Display the current process context

## General command format:

```
!process -1 [flags]
```

e.g.

    0: kd> !process -1 0
    PROCESS ffffcd0894a85040
        SessionId: none  Cid: 0004    Peb: 00000000  ParentCid: 0000
        DirBase: 001ad000  ObjectTable: ffff9f8502276e40  HandleCount: 2371.
        Image: System

## Listing all processes:

General command format:

`!process 0 [flags]
`
    2: kd> !process 0 0
    **** NT ACTIVE PROCESS DUMP ****
    PROCESS ffffe409bc285040
        SessionId: none  Cid: 0004    Peb: 00000000  ParentCid: 0000
        DirBase: 001ad000  ObjectTable: ffffbe0867c76e40  HandleCount: 2214.
        Image: System

    PROCESS ffffe409bc2dc080
        SessionId: none  Cid: 006c    Peb: 00000000  ParentCid: 0004
        DirBase: 00392000  ObjectTable: ffffbe0867c22b80  HandleCount:   0.
        Image: Registry

    PROCESS ffffe409bcf5d040
        SessionId: none  Cid: 015c    Peb: 38cf106000  ParentCid: 0004
        DirBase: 0f0f5000  ObjectTable: ffffbe08692134c0  HandleCount:  53.
        Image: smss.exe
    ...

## Search for the process context based on executable name:

General command format:

`!process 0 [flags] [exe name]`

e.g.

    0: kd> !process 0 0 Explorer.exe
    PROCESS ffffd889e8c86340
        SessionId: 1  Cid: 13b0    Peb: 0062c000  ParentCid: 1370
        DirBase: 26a9ee000  ObjectTable: ffffc80fb8086f00  HandleCount: 2164.
        Image: explorer.exe

## Search for the process context based on executable process ID (PID):

General command format:

`!process [PID] [flags]
`
Where the Process ID can be found with tools like Task Manager.

e.g.

    0: kd> !process 0x13b0 0
    Searching for Process with Cid == 13b0
    PROCESS ffffd889e8c86340
        SessionId: 1  Cid: 13b0    Peb: 0062c000  ParentCid: 1370
        DirBase: 26a9ee000  ObjectTable: ffffc80fb8086f00  HandleCount: 2164.
        Image: explorer.exe

Note that the process ID (PID) given there, 0x13b0, was found via the above !process 0 0 explorer.exe command, using the “Cid” field as the PID.

## Change process context to target executable:

General command format:

`.process /i /r /p ["PROCESS" address from !process output]`

e.g.

    0: kd> .process /i /r /p ffffd889e8c86340
    You need to continue execution (press 'g' <enter>) for the context
    to be switched. When the debugger breaks in again, you will be in
    the new process context.
    0: kd> g
    Break instruction exception - code 80000003 (first chance)
    nt!DbgBreakPointWithStatus:
    fffff802`6bc07d20 cc              int     3
    0: kd> !process -1 0
    PROCESS ffffd889e8c86340
        SessionId: 1  Cid: 13b0    Peb: 0062c000  ParentCid: 1370
        DirBase: 26a9ee000  ObjectTable: ffffc80fb8086f00  HandleCount: 2164.
        Image: explorer.exe

Again, note the memory address ffffd889e8c86340 for the process context was taken from the above search commands.

At this point you can also use the list modules command lm u to list only the userspace modules in the explorer.exe context, after forcing the load of userspace symbols with .reload /f (which will take a while to download userspace executables’ symbols from Microsoft’s symbol server.)

    0: kd> .reload /f
    Connected to Windows 10 19041 x64 target at (Sun Nov 10 10:49:12.753 2024 (UTC - 5:00)), ptr64 TRUE
    Loading Kernel Symbols
    ...............................................................
    ................................................................
    ..............................................
    Loading User Symbols
    ......

    Press ctrl-c (cdb, kd, ntsd) or ctrl-break (windbg) to abort symbol loads that take too long.
    Run !sym noisy before .reload to track down problems loading symbols.

    ..........................................................
    ................................................................
    ................................................................
    ................................................................
    .......................
    Loading unloaded module list
    ...................

    ************* Symbol Loading Error Summary **************
    Module name            Error
    SharedUserData         No error - symbol load deferred
    clipsp                 The system cannot find the file specified
    vsock                  The system cannot find the file specified
    vmci                   The system cannot find the file specified
    <cut>

    You can troubleshoot most symbol related issues by turning on symbol loading diagnostics (!sym noisy) and repeating the command that caused symbols to be loaded.
    You should also verify that your symbol search path (.sympath) is correct.
    0: kd> lm u
    start             end                 module name
    00007ff6`fcd00000 00007ff6`fd2a1000   Explorer   (pdb symbols)          C:\ProgramData\dbg\sym\explorer.pdb\3F47A08E5D6CF131EDFEE596D8394B321\explorer.pdb
    00007fff`0c130000 00007fff`0c21e000   ieproxy    (pdb symbols)          C:\ProgramData\dbg\sym\IEProxy.pdb\DF20FA99EE799987D60B9B161935AC441\IEProxy.pdb
    00007fff`0c220000 00007fff`0c2fe000   werconcpl   (pdb symbols)          C:\ProgramData\dbg\sym\werconcpl.pdb\54B4B6668939FBE87034323A2B84DBD91\werconcpl.pdb

