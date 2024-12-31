---
draft: false
date: '2024-12-31T15:29:56Z'
lastmod: '2024-12-31T15:29:56Z' # Optional; will be updated by GitInfo if enabled
title: 'AutoPilot Setup Suggestions'
description: "Quick fire suggestions for setting up Autopilot"
summary: "Some quick fire suggestions for setting up Autopilot"
author: "BassJamm"
tags: ["new"]
showToc: true
hidemeta: false
canonicalURL: "https://canonical.url/to/page"
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
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/BassJamm/BassJamm.github.io/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

Intune Autopilot is a powerful tool for building and deploying Windows 11 devices, letting you deploying configurations and settings whilst the machine is building and on-boarded to EntraID and Intune.

Below are some quick suggestions and notes from what I have learned about setting it up and managing it.

## Quick Fire Bullet Points

+ Get your OEM to import devices for you!
+ Don't forget about setting a BIOS password, it is easy to forget this step until it is to late in the process.
+ Include SSPR in the remit of your Intune project, it saves so much time.
+ Don't forget LAPS.
+ Setup 3 dynamic groups in Entra ID to do the following.
  + 1st on to capture all Intune Devices.
  + 2nd one to capture all Intune Users.
  + 3rd one to capture all Autopilot Devices.
+ Create a dynamic group to capture all devices by targetting a device category, this is useful to have config and apps apply after Autopilot.
+ Create a Conditional Access policy, that will only allow access if the deivces is policy compliant, make sure this is applied to the all intune users dynamic group.
+ Extend the compliancy to longer than in production to aid in testing and longer deployment times.
+ Check your Configuration Profile settings do not conflict with the Security Baseline policies.

I have expanded on some below.

## Dynamic Groups

I have suggestd 4 groups in the above list, below are the reasons for them and the dynamic query suggestions.

> Configuring groups as Dynamic Device groups automates the process of adding devices to the appropriate group, reducing administrative overhead.
> Check out this [Microsoft documentation](https://learn.microsoft.com/en-us/autopilotenrollment-autopilot#create-an-autopilot-device-group-using-intune) for reference.

+ To create a group that includes all of your Autopilot devices, enter: `(device.devicePhysicalIDs -any (_ -contains "[ZTDID]"))`, the [ZTDID] field identifies Zero-Touch Deployment IDs for your devices.
+ To create a group that includes all Autopilot devices with a specific group tag (the Azure AD device OrderID), enter: `(device.devicePhysicalIds -any (_ -eq "[OrderID]:179887111881"))`, the OrderID refers to the Azure AD device's Order ID.
  + To create a group that includes all your Autopilot devices with a specific Purchase Order ID, enter: `(device.devicePhysicalIds -any (_ -eq "[PurchaseOrderId]:76222342342"))`.
+ To create a group that collects devices based on their category, try, `(device.deviceCategory -eq "IT")`. This group will only find devices after the user selects the category in the Company Portal or an Admin assigns the machine on from Intune. Apply patching, Apps or config you want to apply after Autopilot has done it's bit.

## BIOS Password

Painfully, this often has to be done manually by someone who is provisioning the machine however, some brands do offer command line tools to do this. Lenovo is one example of this and Dell also offer this via their Dell Software.

I'll update this same post as I find more, hope it helps someone!
