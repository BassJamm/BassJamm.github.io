---
draft: false
date: '2024-12-31T16:00:46Z'
lastmod: '2024-12-31T16:00:46Z' # Optional; will be updated by GitInfo if enabled
title: 'DeployEveryOnePrint'
description: "Deploy EveryOne Print from Intune"
summary: "Deploy EveryOne Print from Intune"
author: "BassJamm"
tags: ["Intune"]
categories: ["How To"]
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
Local Folder Structure
>
> I would suggest setting up the following folder struture on your local machine first.
>
>1. Create `C:\Software\Intune-Apps\EveryOnePrint`.
>2. Inside EveryOnePrint, place your exe and install script.
>3. Create an output folder here like so, `C:\Software\Intune-Apps\Output`


1. Find your exe file, it should come from your supplier (there's no EveryOne Print website so far as I could fine.)
2. Create a new script, name it `Install-EveryOnePrint.ps1`.
3. Place the script below in your new script file.
4. Change the syntax with your company information.

Package the exe and the script into an IntuneWin File using the Microsoft Win32 Content Prep Tool; command refernce below.

1. Open PowerShell as an Admin.
2. Navigate to the location of the Win32 Content Prep Tool.
3. Run this command, `Content-prep-tool-name -c C:\Software\Intune-Apps\EveryOnePrint -s C:\Software\Intune-Apps\EveryOnePrint\Install-EveryOnePrint.ps1 -o C:\Software\Intune-Apps\Output`

### Deployment Script

```powershell showLineNumbers
<# Install PC Client
    Available command parameters:
        /S – Run the installer in unattended mode
        /GATEWAYADDRESS=xxx – chooses HCP gateway address
        /ACCOUNTDOMAIN=yyy – chooses account domain name
        /SYNCPERIOD=nn – automatic synchronization period, in minutes. The default period is 60 minutes
        /IGNORESSLERRORS=true|false – option indicating whether to ignore any errors related to SSL handshake (for example wrong certificate or host name). The default value is false
        /SYNCDRIVER=true|false – enable or disable automatic driver installation. Disabling assumes the user is responsible for the driver install. The default value is true
        /IPPOVERSSL=true|false – enable or disable printing over secure SSL connection. The default value is false
        /AUTHTYPE=0|1|2 – User authentication type: 0=username from session (default), 1=user name from session + domain name, 2=manual login, 3=UserPrincipalName
        /ALLOWCONFIGURATION=true|false – enable or disable the ability for the end-user to configure the PC client after installation. The default value is true
#>

<#  2) Amend below command with values and parameters for your installation:
        GATEWAYADDRESS=
        /ACCOUNTDOMAIN=
        /AUTHTYPE=0
        /SYNCDRIVER=true
        /IPPOVERSSL=true
        /ALLOWCONFIGURATION=false
#>
Start-Process .\hcpclient-3.26.0-release-setup.exe -ArgumentList "/S /GATEWAYADDRESS="<# Look in notes above#>" /ACCOUNTDOMAIN="<# Look in notes above#>" /AUTHTYPE=0 /SYNCDRIVER=true /IPPOVERSSL=true /ALLOWCONFIGURATION=false" -Verb RunAs
```
