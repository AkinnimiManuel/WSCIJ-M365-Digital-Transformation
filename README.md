# 🏢 WSCIJ Microsoft 365 Digital Transformation

This project documents my hands-on work as a **Microsoft 365 Administrator** for the **Wole Soyinka Centre for Investigative Journalism (WSCIJ)**, focused on improving collaboration, data security, and system efficiency using the Microsoft 365 ecosystem.

## 🔍 Overview

WSCIJ previously relied on manual file sharing and unsecured access methods for internal operations.  
To modernize IT workflows, I led the implementation and administration of **Microsoft 365 services**, including **SharePoint Online**, **Exchange Online**, **Teams**, **Intune**, and **Microsoft Entra ID (Azure AD)**.  

The result was a secure, collaborative, and cloud-driven environment optimized for remote and hybrid work.


## 🎯 Objectives

- Centralize document management across departments using **SharePoint Online**
- Improve collaboration through **Microsoft Teams** and **OneDrive**
- Strengthen access security using **Microsoft Entra ID (Azure AD)** and **MFA enforcement**
- Manage and secure user devices via **Microsoft Intune**
- Automate onboarding/offboarding workflows
- Ensure data protection and compliance with backup and DLP policies

---

## 🧰 Tools & Technologies

| Category | Tools / Services |
|-----------|------------------|
| Identity & Access | Microsoft Entra ID, MFA, Conditional Access |
| Device Management | Microsoft Intune, Endpoint Security Policies |
| Collaboration | Microsoft Teams, SharePoint, OneDrive |
| Communication | Exchange Online, Outlook |
| Automation | PowerShell, Power Automate |
| Monitoring | Microsoft 365 Admin Center, Defender XDR |
| Backup & Compliance | OneDrive Backup, Microsoft Purview, Versioning |
| Documentation | SOPs, SharePoint Wiki, Knowledge Base |

---

## ⚙️ Implementation Steps

### 1. Identity & Access Management
- Created and organized users, groups, and roles in **Entra ID**
- Enforced **Multi-Factor Authentication (MFA)** for all staff
- Implemented **Conditional Access Policies** to protect sign-ins
- Configured **Self-Service Password Reset (SSPR)**

### 2. Device & Endpoint Management
- Enrolled all staff laptops into **Intune**
- Deployed compliance and configuration profiles
- Applied security baselines (antivirus, firewall, BitLocker)
- Enabled remote wipe for lost or decommissioned devices

### 3. SharePoint & Teams Setup
- Designed departmental sites for **HR**, **Communications**, and **Programmes**
- Created libraries with custom permission levels
- Set up **Teams channels** mapped to departments for easier collaboration
- Integrated Teams with SharePoint document storage

### 4. Exchange & Communication
- Configured **shared mailboxes** and **distribution groups**
- Migrated user mail data and applied retention policies
- Managed signatures and mailbox forwarding rules via PowerShell

### 5. Automation & Support
- Automated **user onboarding/offboarding** using Power Automate
- Maintained SOPs, user guides, and knowledge base for internal support
- Monitored system health and audit logs weekly via Admin Center

---

## 📈 Outcomes

- ✅ Reduced system downtime by **30%** through proactive patching and monitoring  
- ✅ Improved collaboration and file access for over **25 team members**  
- ✅ Strengthened data security with MFA and conditional access enforcement  
- ✅ Simplified onboarding/offboarding, saving **2+ hours per user**  
- ✅ Centralized documentation and compliance alignment across all departments  

---

## 📂 Repository Contents

| Folder | Description |
|--------|-------------|
| `/screenshots` | Visuals of admin configurations, dashboards, and SharePoint pages |
| `/scripts` | PowerShell scripts for automation (e.g., user creation, mailbox rules) |
| `/documentation` | Internal SOPs, architecture diagrams, and policy documents |

---

## 🧾 Sample Scripts

### 🔹 Bulk User Creation (PowerShell)
```powershell
Import-Csv "new_users.csv" | ForEach-Object {
    New-MsolUser -UserPrincipalName $_.Email -DisplayName $_.Name -UsageLocation "NG" -LicenseAssignment "M365_BUSINESS_PREMIUM"
}
```

### 🔹 Mailbox Forwarding Rule
```powershell
Set-Mailbox -Identity "exstaff@wscij.org" -ForwardingSMTPAddress "supervisor@wscij.org" -DeliverToMailboxAndForward $true
```


🧠 Lessons Learned

Importance of consistent documentation and SOP updates

How Intune compliance directly improves endpoint security posture

Leveraging PowerShell automation to manage repetitive admin tasks

Real-world troubleshooting experience across Entra ID, Intune, and Exchange Online

📎 Author

Emmanuel Stefan
Microsoft 365 Administrator | IT Support Specialist
🌐 [LinkedIn](www.linkedin.com/in/akinnimi-stefan)
 | 📫 akinnimimanuel@gmail.com
