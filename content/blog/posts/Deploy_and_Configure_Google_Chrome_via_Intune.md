---
title: "Deploy and Configure Google Chrome via Intune"
date: 2026-11-01
description: "How to Deploy and Configure Google Chrome using intune."
tags:
  - Intune
---

> [!NOTE]
> This information is in a short format, TL;DR.

## Testing Chrome Policies Locally

Review the below for the key info for testing Chrome Policies locally before deploying via Intune.



{{< accordion mode="closed" separated=true >}}
  {{< accordionItem title="Registry Key Locations" open=false >}}

  ### Device-level policies (HKLM)

  Machine-level policies apply to all users on the device and are stored under:

  ```powershell
  HKLM:\SOFTWARE\Policies\Google\Chrome
  ```

  ### User-level policies (HKCU)

  User-level policies apply only to the currently signed-in user and are stored under:

  ```powershell
  HKCU:\SOFTWARE\Policies\Google\Chrome
  ```

  ### Viewing configured policies

  To view the machine-level Chrome policies configured on a device, run:

  ```powershell
  Get-ItemProperty 'HKLM:\SOFTWARE\Policies\Google\Chrome'
  ```

  To view user-level policies, run:

  ```powershell
  Get-ItemProperty 'HKCU:\SOFTWARE\Policies\Google\Chrome'
  ```

  {{< /accordionItem >}}

  {{< accordionItem title="Finding Policy Names" open=false >}}

  1. Open Chrome.
  2. In address bar, type, `Chrome://Policy' and press Enter.
  3. Here, tick the box in the top right to show policies without settings.
  4. Click on any item name to discover more about the setting and options.


  {{< /accordionItem >}}

  {{< accordionItem title="Adding and Removing Settings" open=false >}}

  ### Adding\removing settings for local testing


  ```powershell
  $PolicyPath = 'HKLM:\SOFTWARE\Policies\Google\Chrome'

  # Set policy
  Set-ItemProperty -Path $PolicyPath -Name 'IncognitoModeAvailability' -Value 1 -Type DWord

  # Remove policy (return to default)
  Remove-ItemProperty -Path $PolicyPath -Name 'IncognitoModeAvailability' -ErrorAction SilentlyContinue
  ```

  {{< /accordionItem >}}

{{< /accordion >}}

---

## Deploying Google Chrome via Intune (MSI)

Review the below for deploying Chrome from Intune.

{{< accordion mode="closed" separated=true >}}
  {{< accordionItem title="Installation" open=false >}}

  1. Download the 64-bit Enterprise MSI from [Google Chrome Enterprise](https://chromeenterprise.google/browser/download/).
  2. In Intune, navigate to **Apps → Windows → Add**.
  3. Select **Custom App (Win32)**.
  4. Upload the MSI and configure the application details not picked up automatically or you want to customise.
  5. Assign to the required device or user groups.
  Intune handles installation automatically. Silent installation command (if required):

  ```powershell
  msiexec /i "GoogleChromeStandaloneEnterprise64.msi" /qn /norestart
  ```
  {{< /accordionItem >}}

  {{< accordionItem title="Detection" md=false open=false >}}
  Intune uses the MSI product code to detect installation.
  {{< /accordionItem >}}

  {{< accordionItem title="Updates" md=false open=false >}}
  Chrome updates automatically through Google Update. No regular MSI redeployment is required.
  {{< /accordionItem >}}

  {{< accordionItem title="Configuration" md=false open=false >}}
  Deploy Chrome settings separately using an Intune Settings Catalog profile.
  {{< /accordionItem >}}

{{< /accordion >}}

## Useful Settings to start with

{{< accordion mode="closed" separated=true >}}
  {{< accordionItem title="Suggestions for settings" open=false >}}
  ```powershell

# Google Chrome - Machine Policy Configuration

$PolicyPath = 'HKLM:\SOFTWARE\Policies\Google\Chrome'

# Create registry key if it doesn't exist
if (!(Test-Path $PolicyPath)) {
    New-Item -Path $PolicyPath -Force | Out-Null
}

# Helper function to set Chrome policies
function Set-ChromePolicy {
    param (
        [string]$Name,
        [object]$Value,
        [ValidateSet('DWord', 'String', 'MultiString')]
        [string]$Type = 'DWord'
    )

    New-ItemProperty -Path $PolicyPath -Name $Name -Value $Value -PropertyType $Type -Force | Out-Null
}

# Approved extension IDs
$ApprovedExtensionIds = @(
    # 'abcdefghijklmnopabcdefghijklmnop'
)

# Approved URL blocklist
$BlockedURLs = @(
    # 'example.com'
)

# High Priority

Set-ChromePolicy 'SafeBrowsingProtectionLevel' 2                 # Enables Enhanced Safe Browsing protection
Set-ChromePolicy 'DownloadRestrictions' 4                        # Blocks malicious and dangerous downloads
Set-ChromePolicy 'SSLErrorOverrideAllowed' 0                     # Prevents users bypassing SSL certificate warnings
Set-ChromePolicy 'SafeBrowsingProceedAnywayAllowed' 0            # Prevents users bypassing Safe Browsing warnings
Set-ChromePolicy 'PasswordManagerEnabled' 0                      # Disables Chrome Password Manager
Set-ChromePolicy 'AutofillAddressEnabled' 0                      # Disables address autofill
Set-ChromePolicy 'AutofillCreditCardEnabled' 0                   # Disables credit card autofill
Set-ChromePolicy 'BrowserGuestModeEnabled' 0                     # Disables Guest browsing
Set-ChromePolicy 'IncognitoModeAvailability' 1                   # Disables Incognito mode
Set-ChromePolicy 'QuicAllowed' 0                                 # Disables the QUIC protocol
Set-ChromePolicy 'RemoteDebuggingAllowed' 0                      # Disables remote debugging
Set-ChromePolicy 'BrowserLegacyExtensionPointsBlocked' 1         # Blocks legacy extension points
Set-ChromePolicy 'DeveloperToolsAvailability' 2                  # Disables Developer Tools
Set-ChromePolicy 'DnsOverHttpsMode' 'off' 'String'               # Disables DNS-over-HTTPS
Set-ChromePolicy 'SyncDisabled' 1                                # Disables Chrome Sync
Set-ChromePolicy 'DynamicCodeSettings' 1                         # Prevents the browser process creating dynamic code
Set-ChromePolicy 'ApplicationBoundEncryptionEnabled' 1           # Enables application-bound encryption for stored secrets
Set-ChromePolicy 'SitePerProcess' 1                              # Enables site isolation for every site
Set-ChromePolicy 'InsecurePrivateNetworkRequestsAllowed' 0       # Blocks insecure requests to more-private network endpoints
Set-ChromePolicy 'CrossOriginWebAssemblyModuleSharingEnabled' 0  # Disables cross-origin WebAssembly module sharing
Set-ChromePolicy 'NativeMessagingUserLevelHosts' 0               # Blocks user-level Native Messaging hosts
Set-ChromePolicy 'AuthSchemes' 'ntlm,negotiate' 'String'         # Restricts HTTP authentication to NTLM and Negotiate
Set-ChromePolicy 'AIModeSettings' 1                              # Disable Google's AI Mode integrations in the address bar and New Tab page search box
Set-ChromePolicy 'AutofillGenAiSettings' 2                       # Disable Google's Settings for generative AI features in Autofill

# Block all extensions by default
Set-ChromePolicy 'ExtensionInstallBlocklist' @('*') 'MultiString' # Blocks all extensions unless explicitly allowlisted

# Allow approved extensions
if ($ApprovedExtensionIds.Count -gt 0) {
    Set-ChromePolicy 'ExtensionInstallAllowlist' $ApprovedExtensionIds 'MultiString' # Allows approved extensions
}

# Block specified URLs
if ($BlockedURLs.Count -gt 0) {
    Set-ChromePolicy 'URLBlocklist' $BlockedURLs 'MultiString'    # Blocks specified website URLs
}

# Medium Priority

Set-ChromePolicy 'BrowserSignin' 1                               # Allows users to sign in to Chrome
Set-ChromePolicy 'SafeBrowsingForTrustedSourcesEnabled' 1        # Enables Safe Browsing for trusted sources
Set-ChromePolicy 'EncryptedClientHelloEnabled' 0                 # Disables TLS Encrypted ClientHello
Set-ChromePolicy 'EnableUnsafeSwiftShader' 0                     # Disables software WebGL fallback using SwiftShader
Set-ChromePolicy 'HttpsOnlyMode' 1                               # Enables HTTPS-only mode
Set-ChromePolicy 'MetricsReportingEnabled' 0                    # Disables usage metrics reporting
Set-ChromePolicy 'DefaultPopupsSetting' 2                        # Blocks pop-ups by default
Set-ChromePolicy 'DefaultGeolocationSetting' 2                   # Blocks location access by default
Set-ChromePolicy 'DefaultNotificationsSetting' 2                 # Blocks notifications by default
Set-ChromePolicy 'DefaultWebBluetoothGuardSetting' 2             # Blocks Web Bluetooth access by default
Set-ChromePolicy 'DefaultWebUsbGuardSetting' 2                   # Blocks Web USB access by default

# Low Priority

Set-ChromePolicy 'NTPContentSuggestionsEnabled' 0                # Disables New Tab page content suggestions
Set-ChromePolicy 'BackgroundModeEnabled' 0                       # Prevents Chrome running background apps after closing
Set-ChromePolicy 'ImportSavedPasswords' 0                        # Prevents importing saved passwords
Set-ChromePolicy 'ImportAutofillFormData' 0                      # Prevents importing autofill form data
Set-ChromePolicy 'PrintingEnabled' 0                             # Disables printing
Set-ChromePolicy 'TranslateEnabled' 0                            # Disables page translation

# Display configured policies
Get-ItemProperty $PolicyPath | Format-List


  ```

  {{< /accordionItem >}}
{{< /accordion >}}