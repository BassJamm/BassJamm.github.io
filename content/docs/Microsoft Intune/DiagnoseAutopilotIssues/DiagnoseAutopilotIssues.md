---
title: "Diagnose Autopilot Issues"
date: 2024-12-28T22:11:56Z
last_modified: 2024-12-28T22:11:56Z
draft: false
description: ""
edit_with_github_link: "https://github.com/your-repo/edit/main/content/docs/DiagnoseAutopilotIssues.md"
---

## Get Autopilot Diagnostics

1. Press `Shift + F10` or  `Fn + Shift + F10`, to open Command Prompt.
2. Type "PowerShell", to launch the PowerShell prompt.
3. Type , `New-Item -Path C:\ -Name Temp -ItemType Directory` to create the destination folder for the logs.
4. Type,` Set-ExecutionPolicy -ExecutionPolicy Unrestricted` to allow the script install later.
5. Make sure your location is, `"C:\Windows\System32`", if not type  `sl C:\Windows\System32\`.
6. Run, `MdmDiagnosticsTool.exe -area Autopilot -zip C:\Temp\mdmDiag.zip`.
7. Run, `Install-Script -Name Get-AutopilotDiagnostics -Scope CurrentUser`.
8. Run, `Get-AutopilotDiagnostics.ps1 -zip C:\Temp\mdmdiag.zip`.
9. Scroll down the console and find the Timeline section and check the items in the list for the failed app.

## Autopilot Process stuck for long period of time

Get the Autopilot logs from the device, [[#Get Autopilot Diagnostics]].

### Finding an App Id

Substitute appid into the URL below, include the `*`, where they are now.
`https://endpoint.microsoft.com/#blade/Microsoft_Intune_Apps/SettingsMenu/2/appId/**appid**`

## User assigned during pre-prevision

[See Microsoft document section](https://learn.microsoft.com/en-us/autopilot/tutorial/user-driven/hybrid-azure-ad-join-assign-device-to-user#assign-autopilot-device-to-a-user-optional)

Unassign the user to clear the assignment.

## Hardware Error during Pre-Provision

Error message, autopilot securing your hardware 0x800705b4

[[TPM Attestation error 0x800705b4]]

## Restart Autopilot Process

### Sysprep

>[!NOTE]
>If the pre-provision process has already started, it will continue from where it got to. See factory reset below to stop the device.

`C:\Windows\System32\Sysprep\sysprep.exe`
Press Enter with the default settings.

### Factory Reset

>[!WARNING]
>This'll prompt to clear the TPM chip, there's no harm in doing this, it just adds time and an extra 1\2 reboots.

This uses the Windows UI reset options.
`systemreset –factoryreset`