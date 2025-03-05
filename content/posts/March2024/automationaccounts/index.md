---
draft: true
date: '2025-03-05T22:00:18Z'
lastmod: '2025-03-05T22:00:18Z' # Optional; will be updated by GitInfo if enabled
title: 'Automating Scripts in Azure'
description: "Automating your first script in Azure"
summary: "Summary"
author: "BassJamm"
tags: ["Azure", ""]
categories: ["How To"]
showToc: true
TocOpen: true
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

## Quick Intro

A year or so ago, I dabbled with Automation Accounts but ran into enough frustrations that I opted to host scripts
on a Windows Server instead. Recently, I decided to give it another shot, needing to get something up and running
without an appropriate Windows machine being available. This time, whether the platform has improved or I was the
problem all along, everything went much more smoothly.

## Why the need to do this?

I've used SCCM in the past to manage endpoints, and it provides a wide range of reports, covering everything from updates
and antivirus status to application installs and more. While it's definitely possible to retrieve similar data from Intune,
my requirement was different: I needed a way to access these reports without having to log in to retrieve them.

The reports I'm after are the Detected Apps report and a report of apps installed per device.

## What Needs Doing

Here are the steps I took (though there are likely better ways—so don’t take this as gospel):

- Set up an Automation Account.
- Install the relevant PowerShell modules.
- Create an App Registration to facilitate authentication.
- Create a Runbook, upload the script, and test it.
- Set up the schedule.

### Creating the Automation Account

I'm not going to regurgitate the Microsoft document so, check out the link below for creating an Automation Account.
When/if you do, for what I did, you would not need to setup any identities, I'll outline setting up the same as I did
in a further step.#TODO - Add link to section here.

[Create a standalone Azure Automation account](https://learn.microsoft.com/en-us/azure/automation/automation-create-standalone-account?tabs=azureportal#create-a-new-automation-account-in-the-azure-portal)

### Installing Modules

Below is the listof modules I used; SendGrid is the method I like to use for relaying emails.

- Microsoft.Graph.Authentication.
- #TODO - Add modules.

### Create an App Registration to facilitate authentication

### Create a Runbook, upload the script, and test it

### Set up the schedule
