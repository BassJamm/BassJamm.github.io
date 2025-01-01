---
draft: false
date: '2025-01-01T21:53:04Z'
lastmod: '2025-01-01T21:53:04Z' # Optional; will be updated by GitInfo if enabled
title: 'Sending email with SMTP via Exchange Online using OAuth'
description: "How to use OAuth to send email using Exchange Online"
summary: "Sending email via Exchange Online using OAuth"
author: "BassJamm"
tags: ["Exchange Online", "SMTP"]
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

The below summarises the steps to setup emailing via an account using SMTP with Exchange Online.

 > *I didn't come up with this information, you can find all of it detailed at this [reference link](https://www.emailarchitect.net/eaoauth/kb/azure_application_pop_imap_grant.aspx)*

## Components

- Enterprise App - Which will provide the OAuth token.
- Email Account - Which will be sending the email.
- PowerShell Script - They way in which you can send the mail.

> *I am specifically covering the 2nd option to use an Enterprise Application.*
> *To use OAUTH, the access token is required. There are two ways to retrieve the access token from Microsoft server:*
>
> *User login the account by web browser, the application uses the returned authorization code to request the access token. This way requires user interactive attending, it is not suitable for server-side application.*

## Setup the Enterprise Application

1. Register the application in Azure Portal.
2. Note the `client id` and `tenant id`.
3. Assign API permission to the application.
4. Click Add Permissions.
5. Select `APIs in my organization uses` -> `Office 365 Exchange Online` -> `Application Permission`.
6. Find the relevant permissions of the following, `POP.AccessAsApp permission`, `IMAP.AccessAsApp permission`, `SMTP.AccessAsApp permission`.
7. Grant Admin consent for the Organisation.
8. Create a client secret or Certificate.

## Register SMTP/POP/IMAP service principals in Exchange

> *This will be given permissions to send on behalf of the account you want to use.*

1. Find your `APPLICATION_ID` and `OBJECT_ID`.
2. Open [Exchange Online PowerShell](https://learn.microsoft.com/en-us/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps) to run the cmdlet.
3. `Connect-ExchangeOnline`, this will launch a browser.
4. Create your new service principal, `New-ServicePrincipal -AppId "yourappID" -ServiceId "serviceprincialId"`
5. Query it and check it is there, `Get-ServicePrincipal`.
6. Add the specific mailboxes in the tenant that will be allowed to be access by your application.
7. User is the serviceId.
8. `Add-MailboxPermission -Identity "grant-test@domain.com" -User "serviceprincialId" -AccessRights FullAccess`.

## Enable SMTP Auth per Account

[Microsoft Document Link for where the commands below have come from](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/authenticated-client-smtp-submission#use-exchange-online-powershell-to-enable-or-disable-smtp-auth-on-specific-mailboxes)

```powershell
# Add one of the following to the end of this command, <$true | $false | $null>
Set-CASMailbox -Identity <MailboxId> -SmtpClientAuthenticationDisabled <here>
```

This example enables SMTP AUTH for mailbox `sean@contoso.com`.

```powershell
Set-CASMailbox -Identity sean@contoso.com -SmtpClientAuthenticationDisabled $false
```

This example disables SMTP AUTH for mailbox `chris@contoso.com`.

```powershell
Set-CASMailbox -Identity chris@contoso.com -SmtpClientAuthenticationDisabled $true
```

### Reporting SMTP Auth Status

```powershell
# Gather all mailboxes where SMTP AUTH is disabled
# ? = Where-Object
Get-CASMailbox -ResultSize unlimited | ? {$_.SmtpClientAuthenticationDisabled -eq $true}

# Gather all mailboxes where SMTP AUTH is enabled
Get-CASMailbox -ResultSize unlimited | ? ($_.SmtpClientAuthenticationDisabled -eq $false)

# Gather all mailboxes where the setting is controlled by org settings
Get-CASMailbox -ResultSize unlimited | ? ($_.SmtpClientAuthenticationDisabled -eq $null)

```

## Script to test this out

In-progress
