---
draft: false
date: '2024-12-30T16:05:01Z'
lastmod: '2024-12-30T16:05:01Z' # Optional; will be updated by GitInfo if enabled
title: 'Veeam Backup For Microsoft 365'
description: "Notes on Issues, solutions and Reporting for the product"
summary: "Issues, Solutions, Notes and Reporting"
author: "BassJamm"
tags: ["Veeam", "Microsoft365"]
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
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/BassJamm/BassJamm.github.io/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

## Intro

Veeam Backup for Microsoft 365 is a reliable solution, but after a couple of years now of using and supporting it, I’ve found some areas where I think it needs some improvement.

+ **Built-in Reporting**: I've found these to be a little limited on the details that I needed. Then again I'm a techies who's interested in the read\write of the jobs etc, the reporting out of the box is catered more for end users; you can get 95% of any data you need through Powershell though.
+ **Retention Period Auditing**: There have been some issues (likely due to my own negligence or ignorance) with data falling out of retention without my knowledge. Adding some reporting alongside the backup jobs would be very useful.
+ **SharePoint Script Issues**: Pre-version 8, Microsoft's security settings frequently disabled script execution for backing up web parts. When I queried these with Veeam support, they informed me that these webparts mainly contain meta data and is not something to worry about being backed up.

Some challenges with the product:

+ **Group Ownership Gaps**: Groups without owners aren't backed up unless manually addressed; this is a time consuming one to fix, espcially if you have end users creating groups.
+ **Backup Speed**: Microsoft throttling is punishing sometimes and significantly slows job completion times.
+ **Easy to lose control**: My company adopted the product when it was a comunity product with free support. Since then the best practice guide has been updated and so have the requirements. We've had some considerable issues with our Job layouts, so best review the [best practice guide](https://bp.veeam.com/vb365/) is all I'd say.

Below, I'm sharing what I have found, used and troubleshooted.

## Troubleshooting Scripts

### Checking the Last Backed up Time for an Object

> *The notes below are for Item-Level Backups, which is based on the last modified date of the items within the backups (Emails, Files, Groups etc).*
> *Snapshot level backup retention works the same way accept you lose the whole snapshot not just the items.*

An object is any one of the following item, User, Group, Site, Team, Organization, OneDrive, Mailbox. The script below helps identify when the object was last successfully backed up. Unfortunately, it does not identify which item in the backup is falling out of scope of the retention but, does give an idea of when items will start being removed from the backup job.

For example, in a SharePoint site, the file "Don't Delete Me", was last modified on the 1st of September 2021. If the retention is 3 years, the document would have been removed from the backups 4 months ago, but, other data in that site may have been edited in 2024 and so is still in scope of the retention.

> *I went a bit mad on the `Write-Host` commands in this script but, the staff who'd be running this are not all savvy with Powershell so thought more information was better.*

<script src="https://gist.github.com/BassJamm/b9c5729e3439b69fcf3dc18c6091caf9.js"></script>

### Reviewing the VBO Logs

You can download the logs using the Veeam Console.

1. Open the Console.
2. Click the Hamburger Menu in the top left.
3. Select Help and Support.
4. Select Support Information.
5. With Collect Logs selected, Click next.
6. Select the proxy server your job is running via.
7. Save the logs to a good location and extract them.

The script below doesn't perform many complex actions; it simply removes some square brackets and creates separate date and time variables in their own columns. However, it simplifies the process, making it easier to continue searching, formatting, and exporting the logs from PowerShell.

> *Some of the logs can be over 100,000 lines so there is a progress bar written into the `foreach` loop for some visual feedback.*

<script src="https://gist.github.com/BassJamm/66e23d30075df3fa74a748d4f199e727.js"></script>

### Parsing Job Warnings and Errors

It's quite useful to be able to pull out any warnings or errors from the previous jobs. Below is a script that can help do that.

> *The `starttime` is optional, if you do not provide it, it will provide the most recent job matching the name you provide. Make sure to enter the `starttime` as a string like the example shows.*

<script src="https://gist.github.com/BassJamm/dba4ed04d761c0d0fba96f89d3e4069e.js"></script>

## Removing a User's License

This process can be a bit of a pig, there is a Veeam Blog or KB post [here](https://www.veeam.com/blog/remove-user-license-vbo365.html).

## Best Practices Guide

If you are looking for what Veeam suggest about how to setup and use this solution, there's a guide [here](https://bp.veeam.com/vb365/) for version 7.

### JET Database Errors

> *This will not be an issue once upgraded to version 8 as they have swapped over to Postgresql instead of JET.*

#### Error Example

Example error, `21/03/2024 20:28:39 :: Failed to process site: https://domain.sharepoint.com/sites/sitename. Unable to access repository (E:\*VBOCacheName*\PersistentCache\repository.adb) JetError -1504, JET_errNullInvalid, Null not valid :: 0:03:27`.

#### Solution

Raise a ticket with Veeam first—they’ll guide you in using eseutil.exe (a Microsoft tool for troubleshooting Exchange databases) to check database integrity.

Steps we followed:

+ Ran an integrity check and repair.
+ Deleted all cache files except the database, allowing Veeam to recreate them.
+ Attempted another repair.

Finally, we deleted the cache database and performed a re-sync. Since our database was only 80GB, this was completed within hours. They did warn me that this proess could take days if the `.adb` file is larger.

> *I quote Veeam here, JET errors are notoriously hard to fix without a cache re-sync. They’re typically caused by a forcefully terminated open thread to the database, such as a server reboot.*

### Warning: Possible team chat sync problem was detected

An odd issue quickly fixed by Veeam Support: a mailbox tied to a Microsoft 365 group refused to back up, running for days without completing.

> *Veeam advised against terminating jobs that are running outside the job Window and to allow atleast 2 retries for a job to minimise issues with throttling and JET based errors, see [here for JET Errors](#jet-database-errors).*

#### Log File snippet you should find

```text
[05.08.2024 05:24:55.909]   68 (8380)  Changed items: 0, deleted items: 0, read state changes: 100  
[05.08.2024 05:24:55.909]   68 (8380) Warning: Possible team chat sync problem was detected  
[05.08.2024 05:24:55.914]   47 (5180) Processing mailbox: groupmailboxup@groups.domain.com...  
[05.08.2024 05:24:55.924]   47 (5180) Syncing folder items: Inbox...  
[05.08.2024 05:24:56.004]   47 (5180)  Sync time: 0.0813057  
[05.08.2024 05:24:56.004]   47 (5180)  Changed items: 0, deleted items: 0, read state changes: 100  
[05.08.2024 05:24:56.004]   47 (5180) Warning: Possible team chat sync problem was detected
```

#### Fix

> *Raise a ticket with Veeam first before going and doing anything yourself unless you are happy doing so.*

1. Make sure no jobs or restore sessions are running
2. Stop the Veeam Backup for Microsoft 365 and Veeam Backup Proxy for Microsoft 365 services on the VBO 365 server and all backup proxies.
3. Navigate to `%ProgramData\Veeam\Backup365`.
4. Edit the `proxy.xml` file's source line to include, `FixTeamChatSync="True"`.

```xml
<Veeam>
<Archiver>
<Source FixTeamChatSync="True" />
.
.
.
</Archiver>
</Veeam>
```

## Reporting

As I mentioned to begin with, the reporting is one place that we have had to rely quite heavily on the Powershell commands for. Below are some examples.

### License Overview Report

A simple set of commands that will give you the license overview report for all organisations in your VBO solution.

<script src="https://gist.github.com/BassJamm/a3bde39518387ab1e3db4469e65beb06.js"></script>

Hope this helps someone!
