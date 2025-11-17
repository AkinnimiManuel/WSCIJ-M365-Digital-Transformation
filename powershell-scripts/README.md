# **Microsoft 365 PowerShell Administration Scripts**

*Comprehensive Script Library for M365, Entra ID, Exchange Online, SharePoint, Teams, and Reporting*

This document contains all the PowerShell scripts used in the **WSCIJ Microsoft 365 Digital Transformation Project**.

It is organized into clear sections:

1. Connect to Services
2. User Management
3. Mailbox Management
4. Security & Compliance
5. SharePoint Administration
6. Teams Administration
7. Reporting & Automation

---

# **1️⃣ CONNECT TO M365 SERVICES**

## **1.1 Connect to Exchange Online + Microsoft Graph**

```powershell
Import-Module ExchangeOnlineManagement
Import-Module Microsoft.Graph

Write-Host "Connecting to Exchange Online..."
Connect-ExchangeOnline -UserPrincipalName (Read-Host "Admin UPN")

Write-Host "Connecting to Microsoft Graph..."
Connect-MgGraph -Scopes "User.Read.All","Directory.ReadWrite.All","Group.ReadWrite.All","Mail.ReadWrite","Reports.Read.All"

Get-MgContext
```

## **1.2 Connect to Microsoft Graph (Device Code)**

```powershell
Import-Module Microsoft.Graph
Connect-MgGraph -Scopes "User.Read.All","Directory.ReadWrite.All","Reports.Read.All"
```

---

# **2️⃣ USER MANAGEMENT**

## **2.1 Create a Single User**

```powershell
Param(
  [string]$DisplayName,
  [string]$UserPrincipalName,
  [string]$UsageLocation = "NG"
)

$body = @{
  accountEnabled = $true
  displayName = $DisplayName
  mailNickname = $UserPrincipalName.Split("@")[0]
  userPrincipalName = $UserPrincipalName
  passwordProfile = @{
    password = "P@ssw0rd123!"
    forceChangePasswordNextSignIn = $true
  }
  usageLocation = $UsageLocation
}

New-MgUser -BodyParameter $body
```

---

## **2.2 Bulk Create Users + Assign License**

```powershell
Param(
  [string]$CsvPath = ".\bulk-users.csv",
  [string]$LicenseSku = "M365_BUSINESS_PREMIUM"
)

$sku = Get-MgSubscribedSku | Where-Object { $_.SkuPartNumber -eq $LicenseSku }

Import-Csv $CsvPath | ForEach-Object {
  $upn = $_.UserPrincipalName

  # Create user
  New-MgUser -BodyParameter @{
    accountEnabled = $true
    displayName = $_.DisplayName
    mailNickname = $upn.Split("@")[0]
    userPrincipalName = $upn
    passwordProfile = @{ password = "P@ssw0rd123!"; forceChangePasswordNextSignIn = $true }
    usageLocation = $_.UsageLocation
  }

  # Assign license
  Update-MgUserLicense -UserId $upn -BodyParameter @{
    addLicenses = @(@{ skuId = $sku.SkuId })
    removeLicenses = @()
  }
}
```

---

## **2.3 Assign a License to a User**

```powershell
Param(
  [string]$UserPrincipalName,
  [string]$LicenseSku = "M365_BUSINESS_PREMIUM"
)

$sku = Get-MgSubscribedSku | Where-Object { $_.SkuPartNumber -eq $LicenseSku }

Update-MgUserLicense -UserId $UserPrincipalName -BodyParameter @{
  addLicenses = @(@{ skuId = $sku.SkuId })
  removeLicenses = @()
}
```

---

## **2.4 Reset User Password**

```powershell
Param(
  [string]$UserPrincipalName,
  [string]$NewPassword = "P@ssw0rd123!"
)

Update-MgUser -UserId $UserPrincipalName -BodyParameter @{
  passwordProfile = @{
    password = $NewPassword
    forceChangePasswordNextSignIn = $true
  }
}
```

---

## **2.5 Convert User Mailbox to Shared**

```powershell
Set-Mailbox -Identity user@domain.com -Type Shared
```

---

## **2.6 Disable Account (Offboarding Step)**

```powershell
Update-MgUser -UserId user@domain.com -BodyParameter @{
  accountEnabled = $false
}
```

---

# **3️⃣ MAILBOX MANAGEMENT**

## **3.1 Set Mailbox Quotas**

```powershell
Set-Mailbox -Identity user@domain.com `
 -ProhibitSendQuota 10GB `
 -IssueWarningQuota 9GB
```

---

## **3.2 Grant Full Access to a Mailbox**

```powershell
Add-MailboxPermission -Identity shared@domain.com -User admin@domain.com -AccessRights FullAccess
```

---

## **3.3 Create Shared Mailbox**

```powershell
New-Mailbox -Shared -Name "Support" -Alias "support"
```

---

# **4️⃣ SECURITY & COMPLIANCE**

## **4.1 List MFA Status for All Users**

> Note: MFA via Graph requires Entra Premium P1/P2 access.

```powershell
Get-MgUser -All | Select DisplayName,UserPrincipalName,SignInActivity
```

---

## **4.2 Export Unified Audit Logs (Past 30 Days)**

```powershell
$start = (Get-Date).AddDays(-30)
$end = Get-Date

Search-UnifiedAuditLog -StartDate $start -EndDate $end -ResultSize 5000 |
  Export-Csv "audit-log.csv" -NoTypeInformation
```

---

# **5️⃣ SHAREPOINT ADMINISTRATION**

## **5.1 Create Communication Site (PnP)**

```powershell
New-PnPSite -Type CommunicationSite `
 -Title "Department Policy Repository" `
 -Url "https://tenant.sharepoint.com/sites/policyrepo" `
 -Owner "admin@tenant.onmicrosoft.com"
```

---

## **5.2 Assign Site Admin**

```powershell
Connect-PnPOnline -Url "https://tenant.sharepoint.com/sites/policyrepo" -Interactive
Set-PnPSiteCollectionAdmin -Owners admin@domain.com
```

---

# **6️⃣ TEAMS ADMINISTRATION**

## **6.1 List All Teams**

```powershell
Get-MgTeam -Top 999 | Select DisplayName,Id
```

---

## **6.2 Create a Team**

```powershell
$group = New-MgGroup -BodyParameter @{
  displayName = "IT Support Team"
  mailEnabled = $true
  mailNickname = "itsupport"
  securityEnabled = $false
  groupTypes = @("Unified")
}

New-MgTeam -GroupId $group.Id
```

---

# **7️⃣ REPORTS & AUTOMATION**

## **7.1 License Report**

```powershell
Get-MgUser -All |
  Select DisplayName,UserPrincipalName,@{Name="Licenses";Expression={$_.AssignedLicenses.SkuId}} |
  Export-Csv "license-report.csv" -NoTypeInformation
```

---

## **7.2 Mailbox Size Report**

```powershell
Get-Mailbox -ResultSize Unlimited | 
  Get-MailboxStatistics |
  Select DisplayName,TotalItemSize,ItemCount |
  Export-Csv "mailbox-size-report.csv" -NoTypeInformation
```

---

## **7.3 Export Intune Devices**

```powershell
Invoke-MgGraphRequest -Uri "https://graph.microsoft.com/v1.0/deviceManagement/managedDevices" -Method GET |
  Select-Object -ExpandProperty value |
  ConvertTo-Json | Out-File ".\devices.json"
```

---

## **7.4 Export Inactive Users (90 Days)**

```powershell
$cutoff = (Get-Date).AddDays(-90)

Get-MgUser -All | ForEach-Object {
  $signin = (Get-MgUserAuthenticationSignIn -UserId $_.Id -Top 1 -ErrorAction SilentlyContinue)
  if ($signin -and ([datetime]$signin.createdDateTime -lt $cutoff)) {
    [PSCustomObject]@{
      DisplayName = $_.DisplayName
      UserPrincipalName = $_.UserPrincipalName
      LastSignIn = $signin.createdDateTime
    }
  }
} | Export-Csv "inactive-users.csv" -NoTypeInformation
```

---

