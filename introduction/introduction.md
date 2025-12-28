# Kernel Driver Compilation & Debugging

## WinDbg Kernel Debugging
Open “WinDbg (x64)” on the Debugger VM
Select Menu Bar -> File -> Kernel Debug (ctrl+k)
Put in port 50505, and key a.b.c.d, and hit OK

If you get a Windows Firewall prompt, accept the default options and click “Allow Access”.

If it successfully connects, you should see output like:

Connected to target 172.16.126.22 on port 50505 on local IP 172.16.126.21.
You can get the target MAC address by running .kdtargetmac command.

Then you can hit the pause icon or Menu Bar -> Debug -> Break command to break into the debugger in kernel space.
