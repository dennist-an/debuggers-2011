# Listing Modules

## List Modules (Userspace & Kernel)

The “List Loaded Modules” (lm) command by itself will list both userspace modules (like ntdll.dll) and kernel modules (like win32k.sys), if any are available in the current execution context where the debugger is broken.

    0: kd> lm
    start             end                 module name
    00007ff9`5e170000 00007ff9`5e1a2000   vertdll    (deferred)             
    00007ff9`5e1b0000 00007ff9`5e3a5000   ntdll      (pdb symbols)          c:\symbolcache\ntdll.pdb\4DCEEEEB5B2730B0D0E8BB8617292E611\ntdll.pdb
    ffffa527`5ca00000 ffffa527`5ccd8000   win32kbase   (deferred)             
    ffffa527`5cce0000 ffffa527`5d096000   win32kfull   (deferred)             
    ffffa527`5d0a0000 ffffa527`5d0ea000   cdd        (deferred)             
    ffffa527`5d170000 ffffa527`5d20a000   win32k     (deferred) 
    ...

The “(deferred)” means that the symbols have not been loaded for that module. You can use .reload /f to load them, and then re-run the command, to see which .pdb ("Portable DeBug") symbol file was loaded for the given module.


List Modules Userspace Only

    lm u

If you are in the “System” process context, you will see minimal userspace modules.

    0: kd> lm u
    start             end                 module name
    00007ff9`5e170000 00007ff9`5e1a2000   vertdll    (deferred)             
    00007ff9`5e1b0000 00007ff9`5e3a5000   ntdll      (pdb symbols)          c:\symbolcache\ntdll.pdb\4DCEEEEB5B2730B0D0E8BB8617292E611\ntdll.pdb

But if you are either doing userspace debugging or are in a userspace process context from the kernel debugger you will see all of the userspace modules…
For instance, here’s the output while in the cmd.exe process context:

    1: kd> lm u
    start             end                 module name
    00007ff7`c7930000 00007ff7`c7997000   cmd        (pdb symbols)          c:\symbolcache\cmd.pdb\940C42BA43277A08E065595F774BD4B71\cmd.pdb
    00007ff9`43670000 00007ff9`436a5000   winbrand # (pdb symbols)          c:\symbolcache\winbrand.pdb\448AB91BA247102E475DCF1F9BEDAC231\winbrand.pdb
    00007ff9`5be20000 00007ff9`5bf20000   ucrtbase # (pdb symbols)          c:\symbolcache\ucrtbase.pdb\152B3C4F5E1CE0FE6BC36E9F0F2B10E61\ucrtbase.pdb
    00007ff9`5bf50000 00007ff9`5c218000   KERNELBASE   (pdb symbols)          c:\symbolcache\kernelbase.pdb\6AB9E5243A7CB08BFE2DAB9B577F1DD41\kernelbase.pdb
    00007ff9`5c820000 00007ff9`5cb75000   combase    (private pdb symbols)  c:\symbolcache\combase.pdb\9B1FE38BF66675BED777C60443E58DCD1\combase.pdb
    00007ff9`5d0a0000 00007ff9`5d13e000   msvcrt     (pdb symbols)          c:\symbolcache\msvcrt.pdb\8F2B808E7DA36EB90342FDD8BCA575DB1\msvcrt.pdb
    00007ff9`5da80000 00007ff9`5dbab000   RPCRT4   # (pdb symbols)          c:\symbolcache\rpcrt4.pdb\B5C0210E54BE0924D7A662F0B565B7E41\rpcrt4.pdb
    00007ff9`5dbb0000 00007ff9`5dc6d000   KERNEL32 # (pdb symbols)          c:\symbolcache\kernel32.pdb\0245FB04F377B64F56F6326F4A5A237D1\kernel32.pdb
    00007ff9`5e1b0000 00007ff9`5e3a5000   ntdll      (pdb symbols)          c:\symbolcache\ntdll.pdb\4DCEEEEB5B2730B0D0E8BB8617292E611\ntdll.pdb

We will learn later how the process context can be changed, e.g. to force the kernel debugger to be in the context of a specific userspace process like cmd.exe.

## List Modules Kernel Only

    1: kd> lm k
    start             end                 module name
    ffffa527`5ca00000 ffffa527`5ccd8000   win32kbase   (pdb symbols)          c:\symbolcache\win32kbase.pdb\A65F3558162781E57FAC34189790C6AE1\win32kbase.pdb
    ffffa527`5cce0000 ffffa527`5d096000   win32kfull   (pdb symbols)          c:\symbolcache\win32kfull.pdb\53FBCE4396D1F439C8A85D9FB79695FB1\win32kfull.pdb
    ffffa527`5d0a0000 ffffa527`5d0ea000   cdd        (pdb symbols)          c:\symbolcache\cdd.pdb\16160AC89AC7568482CBD9FD91C5CB061\cdd.pdb
    ffffa527`5d170000 ffffa527`5d20a000   win32k     (pdb symbols)          c:\symbolcache\win32k.pdb\ED706A38659240A066E6FB19B994BAAA1\win32k.pdb
    fffff801`040a0000 fffff801`0432f000   mcupdate_GenuineIntel   (pdb symbols)          c:\symbolcache\mcupdate_GenuineIntel.pdb\2C3A3196EBAFC0B94B0D69BAE95496D91\mcupdate_GenuineIntel.pdb
    fffff801`04330000 fffff801`04336000   hal        (pdb symbols)          c:\symbolcache\hal.pdb\0F693CEBB815CF80A5D486D10B730BA71\hal.pdb

## List Modules Sort Modules (Alphabetically)

    1: kd> lm sm
    start             end                 module name
    fffff801`0a010000 fffff801`0a0dc000   ACPI       (pdb symbols)          c:\symbolcache\acpi.pdb\A48F06B6655ACA705BC313593D8BEC161\acpi.pdb
    fffff801`09f70000 fffff801`09f96000   acpiex     (pdb symbols)          c:\symbolcache\acpiex.pdb\08364240B9FC2D66D5B11FB26F4D527C1\acpiex.pdb
    fffff801`0c150000 fffff801`0c1f6000   afd        (pdb symbols)          c:\symbolcache\afd.pdb\A3A1ED110C58AE975EA5557FB71DF0211\afd.pdb
    fffff801`0c130000 fffff801`0c143000   afunix     (pdb symbols)          c:\symbolcache\afunix.pdb\B517FB73D38EA993408F9ED53788F9FE1\afunix.pdb

## List Modules with Filesystem Path

    1: kd> lm f
    start             end                 module name
    00007ff7`c7930000 00007ff7`c7997000   cmd      C:\Windows\system32\cmd.exe
    00007ff9`43670000 00007ff9`436a5000   winbrand C:\Windows\SYSTEM32\winbrand.dll
    00007ff9`5be20000 00007ff9`5bf20000   ucrtbase C:\Windows\System32\ucrtbase.dll
    00007ff9`5bf50000 00007ff9`5c218000   KERNELBASE C:\Windows\System32\KERNELBASE.dll
    00007ff9`5c820000 00007ff9`5cb75000   combase  C:\Windows\System32\combase.dll
    00007ff9`5d0a0000 00007ff9`5d13e000   msvcrt   C:\Windows\System32\msvcrt.dll

## Combinations of the above

You can combine the above formatting options to e.g. show only kernel modules, sorted alphabetically, with the full filesystem path shown.

    1: kd> lm ksmf
    start             end                 module name
    fffff801`0a010000 fffff801`0a0dc000   ACPI     \SystemRoot\System32\drivers\ACPI.sys
    fffff801`09f70000 fffff801`09f96000   acpiex   \SystemRoot\System32\Drivers\acpiex.sys
    fffff801`0c150000 fffff801`0c1f6000   afd      \SystemRoot\system32\drivers\afd.sys
    fffff801`0c130000 fffff801`0c143000   afunix   \SystemRoot\system32\drivers\afunix.sys
    fffff801`0c5d0000 fffff801`0c61e000   ahcache  \SystemRoot\system32\DRIVERS\ahcache.sys


## List Modules Verbose Output

Add the v option to any lm command to get verbose output.

    1: kd> lm ksmv
    start             end                 module name
    fffff801`0a010000 fffff801`0a0dc000   ACPI       (pdb symbols)          c:\symbolcache\acpi.pdb\A48F06B6655ACA705BC313593D8BEC161\acpi.pdb
        Loaded symbol image file: ACPI.sys
        Image path: \SystemRoot\System32\drivers\ACPI.sys
        Image name: ACPI.sys
        Browse all global symbols  functions  data
        Image was built with /Brepro flag.
        Timestamp:        98F9EEE3 (This is a reproducible build file hash, not a timestamp)
        CheckSum:         000CAB79
        ImageSize:        000CC000
        Translations:     0000.04b0 0000.04e4 0409.04b0 0409.04e4
        Information from resource tables:
    fffff801`09f70000 fffff801`09f96000   acpiex     (pdb symbols)          c:\symbolcache\acpiex.pdb\08364240B9FC2D66D5B11FB26F4D527C1\acpiex.pdb
        Loaded symbol image file: acpiex.sys
        Image path: \SystemRoot\System32\Drivers\acpiex.sys
        Image name: acpiex.sys
        Browse all global symbols  functions  data
        Image was built with /Brepro flag.
        Timestamp:        C8D60B44 (This is a reproducible build file hash, not a timestamp)
        CheckSum:         000302D2
        ImageSize:        00026000
        Translations:     0000.04b0 0000.04e4 0409.04b0 0409.04e4
        Information from resource tables:

# List Modules an Address belongs to

If you’d like to see if a given memory address resides within the address range of a particular module, this can be done with
```
    lm a address
```

    0: kd> lm a fffff801`073feab8
    Browse full module list
    start             end                 module name
    fffff801`07000000 fffff801`08046000   nt         (pdb symbols)          c:\symbolcache\ntkrnlmp.pdb\FC57F1C841C2C3F793D57AC134DC0EFA1\ntkrnlmp.pdb
