# Kernel Debugging

## Set breakpoint
```
bu KmdfHelloWorld!DriverEntry
```

## Install Driver
Ensure the driver is uninstalled first `devcon.exe remove Root\KmdfHelloWorld`, then reinstall the driver.
```
devcon.exe install .\KmdfHelloWorld.inf Root\KmdfHelloWorld
```