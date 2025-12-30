# Break on Module Load

The Set Exception (sx) family of commands, can be used to break when particular events occur. The most important forms are:

    Enable: sxe
    Ignore: sxi

There is a long list of possible exception events, but for now we want to just look at the module load (ld) event. When doing userspace debugging a “module” can be a .exe or .dll. In kernel mode a “module” is a .sys kernel driver (or the technically-a-.exe NT kernel itself.)

## Break on module load

So if you issue the following ("Sexy Load”!) command…

sxe ld

The debugger will break on every module load, as shown in the below video. You will be stopped before any of the code from the driver has executed, thus allowing you to set breakpoints, tweak memory, etc.

## Automatically run command on sx* breaks

Automatically run command on sx* breaks

sx* commands can also take an optional -c "some ; list ; of ; commands" parameter.
A useful command to use is .lastevent, as this will print out information about the last event that occurred. So for instance

    sxe -c ".lastevent" ld

Will print out the name of a module and the address at which it was loaded.

    Last event: Load module KmdfHelloWorld.sys at fffff804`600c0000
    debugger time: Thu Jun 10 10:16:42.225 2021 (UTC - 4:00)
    nt!DebugService2+0x5:
    fffff804`583feb15 cc              int     3

## Notification-only

If you’d just like to see when modules are loaded, but not actually stop and let the human take control (e.g. to just watch what order kernel drivers are loaded at boot time), you can use:

    sxe -c ".lastevent ; g" ld

Example:
```
0: kd> sxe -c ".lastevent ; g" ld
0: kd> g
Last event: Load module KmdfHelloWorld.sys at fffff806`30580000
  debugger time: Mon Dec 29 22:30:24.627 2025 (UTC - 8:00)
Shutdown occurred at (Mon Dec 29 22:30:44.315 2025 (UTC - 8:00))...unloading all symbol tables.

************* Path validation summary **************
Response                         Time (ms)     Location
Deferred                                       cache*C:\SymbolCache
Deferred                                       srv*https://msdl.microsoft.com/download/symbols
Using NET for debugging
Opened WinSock 2.0
Waiting to reconnect...
Connected to target 192.168.150.129 on port 50505 on local IP 192.168.150.128.
You can get the target MAC address by running .kdtargetmac command.
Connected to Windows 10 19041 x64 target at (Mon Dec 29 22:30:50.424 2025 (UTC - 8:00)), ptr64 TRUE
Kernel Debugger connection established.

************* Path validation summary **************
Response                         Time (ms)     Location
Deferred                                       cache*C:\SymbolCache
Deferred                                       srv*https://msdl.microsoft.com/download/symbols
Symbol search path is: cache*C:\SymbolCache;srv*https://msdl.microsoft.com/download/symbols
Executable search path is: 
Windows 10 Kernel Version 19041 MP (1 procs) Free x64
Built by: 19041.1.amd64fre.vb_release.191206-1406
Machine Name:
Kernel base = 0xfffff806`7c21f000 PsLoadedModuleList = 0xfffff806`7ce49770
System Uptime: 0 days 0:00:02.169
Last event: Load module ntoskrnl.exe at fffff806`7c21f000
  debugger time: Mon Dec 29 22:30:50.674 2025 (UTC - 8:00)
Last event: Load module hal.dll at fffff806`7bd60000
  debugger time: Mon Dec 29 22:30:50.705 2025 (UTC - 8:00)
Last event: Load module kdnet.dll at fffff806`7bdd0000
  debugger time: Mon Dec 29 22:30:50.768 2025 (UTC - 8:00)
Last event: Load module kd_02_8086.dll at fffff806`7bd70000
  debugger time: Mon Dec 29 22:30:51.018 2025 (UTC - 8:00)
Last event: Load module mcupdate_AuthenticAMD.dll at fffff806`7bd30000
  debugger time: Mon Dec 29 22:30:51.034 2025 (UTC - 8:00)
Last event: Load module CLFS.SYS at fffff806`7be50000
  debugger time: Mon Dec 29 22:30:51.034 2025 (UTC - 8:00)
Last event: Load module tm.sys at fffff806`7be20000
  debugger time: Mon Dec 29 22:30:51.049 2025 (UTC - 8:00)
Last event: Load module PSHED.dll at fffff806`7bec0000
...
```

## Ignore/Disable breaks

If you used the simple form of sxe ld, it can be disabled via the sxi ld command.

To fully ignore an exception type after you’ve used a -c command on it though, you must use an empty -c command with sxi. E.g.

    sxi -c "" ld    