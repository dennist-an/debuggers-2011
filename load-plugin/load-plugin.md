# Loading third-party plugin

## Obtain a plugin

WinDbg has support for writing extensions, which can significantly improve the analysis of data within the debugger.

    As an example, download a copy of the OST2 fork of the SwishDbg extension (which is used in Arch2001) to your Debugger VM.

    Open it in Visual Studio. When prompted to “Retarget projects” select “OK”. Then right click on the Project and select “Build”.

    Make a note of the full SwishDbgExt.dll path in the Output window printout. It will be something like C:\Users\user\Desktop\swishdbgext-master\Debug\x64\SwishDbgExt.dll depending on where the code was when you opened it.

## Load a plugin

Load the plugin in WinDbg with the !load command as:

    !load <Full_path_to_the_module>

e.g.

    0: kd> !load C:\Users\user\Desktop\swishdbgext-master\Debug\x64\SwishDbgExt.dll
        SwishDbgExt  - Incident Response & Digital Forensics Debugging Extension
        SwishDbgExt Copyright (C) 2018 Comae Technologies DMCC - www.comae.com
        SwishDbgExt Copyright (C) 2014-2018 Matthieu Suiche (@msuiche)

        This program comes with ABSOLUTELY NO WARRANTY; for details type `show w'.
        This is free software, and you are welcome to redistribute it
        under certain conditions; type `show c' for details.

## Unload a plugin

You can later unload the plugin with:

    !unload <Full_path_to_the_module>

## Try out some SwishDbg commands

While !load and !unload work in either userspace or kernel, SwishDbg only works in kernel.

You can see plugin’s available commands either at the repository page or with !SwishDbgExt.help

Note: many of the commands haven’t been updated, and thus have broken on newer versions of Windows.

Some recommendations to try out are:

    !ms_callbacks - Which shows the Microsoft supported callback functions which 3rd party software can use to find out about events on the system such as process creation, registry edits, etc. (These are things which rootkits can either alter or create their own to find out about behavior they want to monitor or hide.)
    !ms_ssdt - This is the System Service Descriptor Table, which is the list of system calls supported by the OS.
    !ms_hivelist - For listing registry hive information.

