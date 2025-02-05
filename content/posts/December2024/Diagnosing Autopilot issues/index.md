---
draft: false
date: '2024-12-29T15:01:27Z'
lastmod: '2024-12-29T15:01:27Z' # Optional; will be updated by GitInfo if enabled
title: 'Diagnosing Autopilot Issues for Windows Devices'
description: "Diagnosing Autopilot Issuesfor Windows Devices"
summary: "Diagnosing Autopilot Issues for Windows Devices"
author: "BassJamm"
tags: ["Intune", "Autopilot"]
categories: ["How To"]
showToc: true
hidemeta: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: true # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/BassJamm/BassJamm.github.io/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

## Collecting Autopilot Diagnostic Logs

> This works for a device that is being built and also after it has been built.

How to collect diagnostic logs from a device that is building currently or has been built.

1. Open Command Prompt with `Shift + F10` or `Fn + Shift + F10`.  
2. Type `PowerShell` and press Enter.
3.  Create a folder for logs, type, `New-Item -Path C:\ -Name Temp -ItemType Directory`.
4. Allow script execution, `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`.
5. Ensure you’re in the correct directory, `sl C:\Windows\System32\`.
6. Run the MDM diagnostics tool, `MdmDiagnosticsTool.exe -area Autopilot -zip C:\Temp\mdmDiag.zip`.

![Running the command](img/mdmdiagnosticTool.png)

7. Install the Diagnostic script, `Install-Script -Name Get-AutopilotDiagnostics -Scope CurrentUser`.
8. Run the diagnostics script, `Get-AutopilotDiagnostics.ps1 -zip C:\Temp\mdmdiag.zip`.

![The output of the steps above](img/OutputofDiags.png)

## Autopilot Process Stuck  

How to check if your Autopilot build is stuck for an extended period.  

First, check the Autopilot Diagnostic Logs using [this section](#collecting-autopilot-diagnostic-logs).

If the diagnostic tool indicates an application that is causing an issue from the output.
Check the section below to the section below to find an application using it's ID.
This has been the issue almost every time from my experience.

> Good thing to remember is not to deploy Win32 Apps along side other types of apps such as Line Of Business apps.
> They will often block each other from installing, deploy only Win32 apps during Autopilot and deploy everything
> else afterwards.

## Finding an Application via it's Id

### The easy way (when it works)

> Bear in mind that this has not worked for a while. There is an error regarding the Enterprise App ID it is trying
> to use.

From step 8 in [this section](#collecting-autopilot-diagnostic-logs). Include the `-Online` switch and it will
ask you to login to the tenant using credentials that have access to Intune. It'll then populate the application IDs
where it can.

### The hard way

You'll need to grab the application ID using [this section](#collecting-autopilot-diagnostic-logs) first,
then substitute your app Id into the url below.

Include the `*`, where they are now.

`https://endpoint.microsoft.com/#blade/Microsoft_Intune_Apps/SettingsMenu/2/appId/**appid**`

> *Worth noting that this method is a bit hit and miss. The alternative is to connect via PowerShell to your tenant.*
> *and generate your own report to collect the application Ids.*

## Device is assigned to a user

You'll see this when you try to complete the user driven method when the username is pre-populated at the initial login prompt.
During pre-provision, where it confirms the Autopilot profile, you'll see the user's email address underneath it.

Check out [this Microsoft doc for more information](https://learn.microsoft.com/en-us/autopilot/tutorial/user-driven/hybrid-azure-ad-join-assign-device-to-user#assign-autopilot-device-to-a-user-optional).
Below is the condensed version.

1. Sign into the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431).
2. Navigate to **Windows | Windows enrollment** screen, under **Windows Autopilot**, select **Devices**.
3. In the **Windows Autopilot devices** screen, locate the device to assign a user to.
4. Once the desired device is located, select the box to the left of the device, making sure that there's check mark in the box, and then select **Assign user** in the toolbar at the top of page.
5. Clear the user from here and click Save.
6. Reboot the device (maybe once or twice) and the user should no longer appear.

## Hardware Error during Pre-Provision

> More errors will be added as and when I find them.

### Autopilot securing your hardware 0x800705b4

[PowerShell script to troubleshoot TPM attestation issues](https://call4cloud.nl/2022/08/the-last-tpm-attestation-script-from-your-lover/)

> *`tpmtool getdeviceinformation` - Gets basic TPM information.*

#### Confirm Attestation Readiness

[Powershell Gallery Link](https://www.powershellgallery.com/packages/Autopilottestattestation/1.0.0.34)

1. Run Command Prompt as an admin.
2. Run the commands below.

```powershell
# Install the module
Install-Module -Name Autopilottestattestation -force
# Set the exetion policy
set-executionpolicy unrestricted
# Import the module
import-module -Name Autopilottestattestation
# Run the report
# Say yes to checking for updates.
test-autopilotattestation
```

Continue on below if you find errors or if you find no errors.

#### Reset the Device to Factory

1. Make sure to delete from MDM (not EntraID or Autopilot).
2. Reset the device from the error page from Pre-provisioning, let this complete.
There is a command for PowerShell for a Windows 11 device, `systemreset --factoryreset`.
3. Try to Pre-Provision again.

#### Clear the TPM Chip

> *There is always a small chance you could bork the machine doing this, don't do it without considering this.*

1. Open command prompt as an admin.
2. Type, `Clear-Tpm`.
3. Run the command, `tpmtool getdeviceinformation` command to ascertain TPM health, [Tool information](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/tpmtool).
4. Try the build again.

#### Escalate to Microsoft

If you have tried everything so far to no end. Now to raise to Microsoft.


Hope this helps someone!
