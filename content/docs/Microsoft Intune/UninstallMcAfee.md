---
title: "Uninstall McAfee"
date: 2024-12-28T22:10:07Z
last_modified: 2024-12-28T22:10:07Z
draft: false
description: ""
edit_with_github_link: "https://github.com/your-repo/edit/main/content/docs/UninstallMcAfee.md"
---

## Creating the Uninstall package

1. Download [McAfee Consumer Product Removal Tool](https://download.mcafee.com/molbin/iss-loc/SupportTools/MCPR/MCPR.exe).
2. Start the downloaded MCPR.exe and then Hold it open…
3. While this dialog is open, navigate to the unpacked source files in:%localappdata%\temp
4. Copy the folder MCPR to a suitable place for packageing, for example c:\temp\McAfeeRemover
5. Close the still open McAfee Software Removal tool by clicking cancel
6. Create a Powershell script in the folder above, for example` C:\temp\McAfeeRemover\McAfeeRemover.ps1`

```powershell
<#  Pre-Reqs

    Follow guidance: https://www.tbone.se/2021/03/05/mcafee-cleanup-with-intune/
    
#>

# Run the cleanup tool
# Remove the older application installation versions of McAfee
$program= ".\McCleanup.exe"
$programArg= "-p StopServices,MFSY,PEF,MXD,CSP,Sustainability,MOCP,MFP,APPSTATS,Auth,EMproxy,FWdiver,HW,MAS,MAT,MBK,MCPR,McProxy,McSvcHost,VUL,MHN,MNA,MOBK,MPFP,MPFPCU,MPS,SHRED,MPSCU,MQC,MQCCU,MSAD,MSHR,MSK,MSKCU,MWL,NMC,RedirSvc,VS,REMEDIATION,MSC,YAP,TRUEKEY,LAM,PCB,Symlink,SafeConnect,MGS,WMIRemover,RESIDUE -v -s"
$process = Start-Process $program -ArgumentList $ProgramArg -passthru -Wait -NoNewWindow

# Remove the Microsoft Store apps from McAfee
$RemoveApp = 'Mcafee'
Get-AppxPackage -AllUsers | Where-Object {$_.Name -Match $RemoveApp} | Remove-AppxPackage
Get-AppxPackage | Where-Object {$_.Name -Match $RemoveApp} | Remove-AppxPackage
Get-AppxProvisionedPackage -Online | Where-Object {$_.PackageName -Match $RemoveApp} | Remove-AppxProvisionedPackage -Online
```

## Create an IntuneWin package with the Content Prep Tool

Catalog files are used to enable Win32 apps in Windows 10 S mode -- they are specific to Application Control which is the mechanism that S Mode uses to enforce and control applications. Lots of details on this at [https://docs.microsoft.com/en-us/mem/intune/apps/apps-win32-s-mode](https://docs.microsoft.com/en-us/mem/intune/apps/apps-win32-s-mode).
