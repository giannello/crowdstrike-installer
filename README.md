# CrowdStrike MSI installer

## Problem

In order to deploy CrowdStrike's Falcon agent on Windows via Google's MDM, it's necessary
to use an MSI file. Other package types are not currently supported.

CrowdStrike's installer is a WiX Bundle (an `.exe` file), which in turn installs multiple `.msi` files.
This makes the usage of MSI wrappers a tricky business - the wrapped MSIs will fail to install, as Windows Installer
allows only one MSI installer to be executed at a given point in time.

## Solution

The solution offered here consists of a WiX installer that deploys the official Falcon installer, and executes it
_after_ the "wrapper" MSI terminates its execution, avoiding the concurrency issue.

## Releases

I will not release pre-built MSIs, as they will contain the CrowdStrike installer. Re-distributing software,
licenses, not my cup of tea - better safe than sorry.

Do not worry, though, as everything you will need to build your MSI is Open Source.

## Great! How do I proceed?

### Building the installer

* Install WiX Toolset's necessary dependencies
  * Go to Control Panel -> Programs -> Turn Windows features on or off -> .NET Framework 3.5
* Install [WiX Toolset](https://wixtoolset.org/releases/) - this was tested on V3.11.2
* Download your CrowdStrike installer (`WindowsSensor.LionLanner.exe`), save it in the same directory as this git
repository
* Build the MSI
```shell
PS C:\Users\giuseppe.iannello\CrowdStrikeInstaller> & 'C:\Program Files (x86)\WiX Toolset v3.11\bin\candle.exe' .\CrowdStrikeInstaller.wxs
Windows Installer XML Toolset Compiler version 3.11.2.4516
Copyright (c) .NET Foundation and contributors. All rights reserved.

CrowdStrikeInstaller.wxs
PS C:\Users\giuseppe.iannello\CrowdStrikeInstaller> & 'C:\Program Files (x86)\WiX Toolset v3.11\bin\light.exe' .\CrowdStrikeInstaller.wixobj
Windows Installer XML Toolset Linker version 3.11.2.4516
Copyright (c) .NET Foundation and contributors. All rights reserved.
```
The `CrowdStrikeInstaller.msi` is ready!

### Using the installer

If you want to test the installation manually, you must invoke the installer via command line, specifying your CID.
No UI is provided to input the CID, so don't even try to double-click on the installer.
```shell
PS C:\Users\giuseppe.iannello\CrowdStrikeInstaller> msiexec.exe /i CrowdStrikeInstaller.msi CID=123456qweasdzxc
```

### Uninstalling

To avoid interfering with CrowdStrike, uninstalling this package will only remove the installer. The Falcon agent will
**not** be removed.
