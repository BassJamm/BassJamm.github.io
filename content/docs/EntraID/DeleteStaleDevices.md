---
title: Delete Stale Devices from EntraID
date: 2024-12-24
---

## Microsoft's Suggestions

[Microsoft Doc: Managing Stale Devices](https://learn.microsoft.com/en-us/entra/identity/devices/manage-stale-devices#detect-stale-devices)

## Detect stale devices

Because a stale device is defined as a registered device that hasn't been used to access any cloud apps for a specific time frame, detecting stale devices requires a timestamp-related property.

## Plan the cleanup of your stale devices

- Define a company policy that will outline what a stale device is to you.
  - Is it a leaver device.
  - Define a time frame that signals a device as stale.
- Autopilot or Universal Print devices should be cleaned up in their respective source locations.
- An account that is equivalent or equal to an Intune or Cloud Device Administrator will be needed.
- When defining your time frame, factor in the window noted for updating the activity timestamp into your value. For example, you shouldn't consider a timestamp that is younger than 21 days (includes variance) as an indicator for a stale device. There are scenarios that can make a device look like stale while it isn't. For example, the owner of the affected device can be on vacation or on a sick leave that exceeds your time frame for stale devices.

{{< callout type="info" >}}
Don't delete system-managed devices. These devices are generally devices such as Autopilot. Once deleted, these devices can't be re-provisioned.
{{< /callout >}}

{{< callout type="warning" >}}
  If your organization uses BitLocker drive encryption, you should ensure that BitLocker recovery keys are either backed up or no longer needed before deleting devices. Failure to do this may cause loss of data.
{{< /callout >}}

## Clean-up Process

{{< callout type="info" >}}
Your Microsoft Entra hybrid joined devices should follow your policies for on-premises stale device management.

Windows 10 or newer devices - Disable or delete Windows 10 or newer devices in your on-premises AD, and let Microsoft Entra Connect synchronize the changed device status to Microsoft Entra ID.

Windows 7/8 - Disable or delete Windows 7/8 devices in your on-premises AD first. You can't use Microsoft Entra Connect to disable or delete Windows 7/8 devices in Microsoft Entra ID. Instead, when you make the change in your on-premises, you must disable/delete in Microsoft Entra ID.
{{< /callout >}}

Complete the process in two phases.

- Phase 1: Disable devices.
- Phase 2: Delete devices.

*Autopilot devices should be out of scope, they cannot be deleted from EntraID.*

### Get the list of devices

```powershell
Get-MgDevice -All | select-object -Property AccountEnabled, DeviceId, OperatingSystem, OperatingSystemVersion, DisplayName, TrustType, ApproximateLastSignInDateTime | export-csv devicelist-summary.csv -NoTypeInformation
```

For a large number of devices you can filter by time stamp to reduce the devices.

```powershell
$dt = (Get-Date).AddDays(-90)
Get-MgDevice -All | Where {$_.ApproximateLastSignInDateTime -le $dt} | select-object -Property AccountEnabled, DeviceId, OperatingSystem, OperatingSystemVersion, DisplayName, TrustType, ApproximateLastSignInDateTime | export-csv devicelist-olderthan-90days-summary.csv -NoTypeInformation
```

### Set devices to disabled

```powershell
$dt = (Get-Date).AddDays(-90)
$params = @{
	accountEnabled = $false
}

$Devices = Get-MgDevice -All | Where {$_.ApproximateLastSignInDateTime -le $dt}
foreach ($Device in $Devices) { 
   Update-MgDevice -DeviceId $Device.Id -BodyParameter $params 
}
```

### Delete devices

{{< callout type="warning" >}}
The Remove-MgDevice cmdlet does not provide a warning. Running this command will delete devices without prompting. There is no way to recover deleted devices.
{{< /callout >}}

{{< callout type="info" >}}
Before administrators delete any devices, back up any BitLocker recovery keys you might need in the future. There's no way to recover BitLocker recovery keys after deleting the associated device.
{{< /callout >}}

```powershell
$dt = (Get-Date).AddDays(-120)
$Devices = Get-MgDevice -All | Where {($_.ApproximateLastSignInDateTime -le $dt) -and ($_.AccountEnabled -eq $false)}
foreach ($Device in $Devices) {
   Remove-MgDevice -DeviceId $Device.Id
}
```