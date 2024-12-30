---
draft: false
date: '2024-12-30T17:25:16Z'
lastmod: '2024-12-30T17:25:16Z' # Optional; will be updated by GitInfo if enabled
title: 'Uninstall McAfee'
description: "How to Uninstall McAfee using a script"
summary: "Uninstall McAfee during Autopilot"
author: "BassJamm"
tags: ["Autopilot", "Intune"]
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

## Creating the Uninstall package

> *Note that the download link to the McAfee Consumer Product Removal Tool will download the latest version.*
> *I had to download an older version I found from a reddit post for this to work; using the latest one is a bit hit and miss.*

1. Download [McAfee Consumer Product Removal Tool](https://download.mcafee.com/molbin/iss-loc/SupportTools/MCPR/MCPR.exe).
2. Start the downloaded `MCPR.exe` and then Hold it open by not pressing next or close.
3. While this dialog is open, navigate to the unpacked source files in, `%localappdata%\temp`.
4. Copy the folder `MCPR` to a suitable place for packageing, for example `c:\temp\McAfeeRemover`.
5. Close the still open McAfee Software Removal tool by clicking cancel.
6. Create a Powershell script in the folder above, for example `C:\temp\McAfeeRemover\McAfeeRemover.ps1`.

<script src="https://gist.github.com/BassJamm/52f0f2eff24dc5cdf86ebd96e26ba63e.js"></script>

## Create an IntuneWin package with the Microsoft Win32 Content Prep Tool

Catalog files are used to enable Win32 apps in Windows 10 S mode, they are specific to Application Control which is the mechanism that S Mode uses to enforce and control applications.

Lots of details on this at https://docs.microsoft.com/en-us/mem/intune/apps/apps-win32-s-mode.
