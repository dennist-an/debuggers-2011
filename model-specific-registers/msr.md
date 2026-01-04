# Model Specific Registers

Model Specific Registers (MSRs) are a mechanism for managing new features as they’re added to Intel hardware. For instance when new security technologies (SMAP/SMEP, SGX, CET, etc) are introduced, there will often be an MSR which controls whether they are enabled or not. MSRs can normally be read and written by the privileged assembly instructions rdmsr, wrmsr. WinDbg commands of the same name exist, and can be used when running the kernel debugger.

## Reading MSRs:

General format:

`rdmsr [MSR #]`

E.g.,

```
3: kd> rdmsr 0xC0000100
msr[c0000100] = 00000000`00000000
```

## Writing MSRs:

General format:

`
wrmsr [MSR #] [value to write]
`

E.g.,
```
3: kd> rdmsr 0xC0000100
msr[c0000100] = 00000000`00000000
3: kd> wrmsr 0xC0000100 0x31337
3: kd> rdmsr 0xC0000100
msr[c0000100] = 00000000`00031337
3: kd> wrmsr 0xC0000100 0
3: kd> rdmsr 0xC0000100
msr[c0000100] = 00000000`00000000
```
