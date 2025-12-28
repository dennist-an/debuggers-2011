# Kernel Driver Debugging: KmdfHelloWorld

## Setting up Debuggee VM (Windows 10)
Configure Debuggee OS

Open an Administrator cmd.exe prompt and run the following commands:

bcdedit /debug on
bcdedit /dbgsettings net hostip:Debugger_VM_IP port:50505 key:a.b.c.d

Where Debugger_VM_IP should be replaced with the IP address of your Debugger VM, and a.b.c.d can be left as-is as it is actually the “secret” key for the debugger and debuggee to talk.

So the final command would look something like: bcdedit /dbgsettings net hostip:172.16.126.22 port:50505 key:a.b.c.d

## Setting up Debugger VM (Windows 10)
Open “WinDbg (x64)” on the Debugger VM
Select Menu Bar -> File -> Kernel Debug (ctrl+k)
Put in port 50505, and key a.b.c.d, and hit OK

If you get a Windows Firewall prompt, accept the default options and click “Allow Access”.

If it successfully connects, you should see output like:

Connected to target 172.16.126.22 on port 50505 on local IP 172.16.126.21.
You can get the target MAC address by running .kdtargetmac command.

Then you can hit the pause icon or Menu Bar -> Debug -> Break command to break into the debugger in kernel space.

This guide outlines the process of connecting a **Debugger VM** to a **Debuggee VM** to install and verify a KMDF driver.

---

### Phase 1: Debugger Initialization

*Perform these steps on the **Debugger VM**.*

1. **Establish Connection**: Connect the WinDbg kernel debugger to the Debuggee.
2. **Enable Verbosity**: Break into the debugger and execute the following command to ensure driver debug prints are visible:
```cmd
ed nt!Kd_IHVDRIVER_Mask 0xf
```
> **Note:** This edits a global variable to set maximum verbosity for IHV drivers.

3. **Resume Execution**: Enter `g` to allow the Debuggee VM to continue running.

---

### Phase 2: Environment Setup

*Perform these steps on the **Debuggee VM**.*

4. **Prepare Directory**: Open an **Administrator Command Prompt** and run:
```cmd
mkdir C:\OST2
cd C:\OST2
```

### Phase 3: File Transfer

*Move the necessary tools and binaries from the **Debugger** to the **Debuggee**.*

5. **Copy Devcon**: Locate `devcon.exe` on the **Debugger** at:
`C:\Program Files (x86)\Windows Kits\10\Tools\x64\devcon.exe`
6. **Copy Driver Files**: Locate the 3 build artifacts on the **Debugger** at:
`C:\Users\user\source\repos\KmdfHelloWorld\x64\Debug\KmdfHelloWorld`
7. **Deploy**: Paste all four files into `C:\OST2` on the **Debuggee VM**.

---

### Phase 4: Driver Installation & Verification

*Perform these steps on the **Debuggee VM** (unless otherwise noted).*

8. **Install Driver**: In the Admin prompt, execute:
```cmd
devcon.exe install .\KmdfHelloWorld.inf Root\KmdfHelloWorld
```


9. **Authorize**: Select **"Install this driver software anyway"** when the security prompt appears.
10. **Verify (Debugger VM)**: Switch to **WinDbg** on the Debugger VM and confirm the following output:
```text
KmdfHelloWorld: DriverEntry  
KmdfHelloWorld: KmdfHelloWorldEvtDeviceAdd
```


*This confirms the driver loaded and executed successfully.*

---

### Phase 5: Cleanup

*Perform this step on the **Debuggee VM**.*

11. **Uninstall**: To remove the driver after testing, run:
```cmd
devcon.exe remove Root\KmdfHelloWorld
```