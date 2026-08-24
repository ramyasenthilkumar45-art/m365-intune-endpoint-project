# 🏢 Enterprise M365, Intune Endpoint & Hybrid Administration Lab

## 📌 Executive Summary
A comprehensive enterprise lab environment built to demonstrate hands-on expertise in Microsoft 365 administration, Intune Modern Management, Hybrid Active Directory provisioning, and IT service desk workflows mapped directly to enterprise job requirements.

---

## 🏗️ Core Architecture & Skills
* **Identity & Access Management:** Microsoft Entra ID (Azure AD), Hybrid Active Directory, RBAC, Conditional Access (MFA Enforce), Group-Based Licensing.
* **Endpoint Management (MDM/MAM):** Microsoft Intune configuration, Compliance Policies, App Protection, and Windows Autopilot profiles.
* **Automation & Scripting:** PowerShell scripts for user onboarding/offboarding and system monitoring.
* **IT Infrastructure & Virtualization:** Windows Server administration, Hyper-V, networking fundamentals, and backup validation.

---

## 🔐 Module 1: Hybrid Identity & M365 Administration

### Step 1: Security Group & Group-Based Licensing
* **Group Name:** `GRP_SG_Remote_Workers`
* **Group Type:** Security Group (Assigned)
* **Assigned License:** Microsoft 365 E5 / Enterprise Mobility + Security E5

### Step 2: Enforce Conditional Access & MFA Policy
* **Policy Name:** `CAP - Enforce MFA for Remote Workers`
* **Target Group:** `GRP_SG_Remote_Workers`
* **Enforced Control:** Require Multifactor Authentication (MFA) for All Cloud Apps.

<br>

**Verification Screenshot:**
<br>
<img src="01-conditional-access.png" alt="Conditional Access Policy" width="750" />

<br>

### 🛠️ Module 1 Troubleshooting Log

#### Issue 01: Conditional Access Policy Creation Blocked by Security Defaults
* **Symptom:** Entra ID threw error: `Security defaults must be disabled to enable Conditional Access policy.`
* **Root Cause:** Microsoft Security Defaults enforce basic baseline security and cannot coexist with custom Conditional Access rules.
* **Resolution:** Navigated to **Entra ID** > **Properties** > **Manage Security defaults**, set to **Disabled**, and selected *Using Conditional Access*.
* **Verification:** Policy `CAP - Enforce MFA for Remote Workers` created successfully.

---

## 🛡️ Module 2: MDM & Endpoint Rollouts (Intune & Security)

### Step 1: Windows 11 Security Compliance Baseline
* **Policy Name:** `POL - Windows 11 Security Baseline`
* **Target Group:** `GRP_SG_Remote_Workers`
* **Enforced Controls:** BitLocker Encryption, Active Windows Firewall, Antivirus Protection.

<br>

**Verification Screenshot:**
<br>
<img src="02-intune-compliance-policy.png" alt="Intune Windows Compliance Policy" width="750" />

<br>



### Step 2: Application Protection Policy (MAM)
* **Policy Name:** `MAM - Windows Corporate App Protection`
* **Target Apps:** Microsoft Teams, Outlook, OneDrive
* **Enforced Controls:** Block data leakage (Prevent Android/Cloud backup, restrict org data transfers to Policy Managed Apps, block unauthorized copy/paste across unmanaged applications).

<br>

**Verification Screenshot:**
<br>
<img src="02-mam-app-protection.png" alt="Intune MAM App Protection Policy" width="750" />

<br>

### Step 3: Windows Autopilot Deployment Profile
* **Profile Name:** `AP - Standard Hybrid/Entra Join Profile`
* **Deployment Mode:** User-Driven
* **Join Type:** Microsoft Entra Joined

<br>
**Verification Screenshot:**
<br>
<img src="02-autopilot-profile.png" alt="Intune MAM App Protection Policy" width="750" />

<br>

### 🛠️ Module 2 Troubleshooting Log

#### Issue 02: Intune Admin Center 503 Gateway Timeout (`DQCancelledOnRequestTimeout`)
* **Symptom:** Portal threw error `Error code 503 / DQCancelledOnRequestTimeout` when attempting to load the Windows 10/11 Compliance Policy creation blade.
* **Root Cause:** Session token expiration or temporary backend microservice timeout in the Intune admin console (`Microsoft_Intune_DeviceSettings`).
* **Resolution Path:**
  1. Closed the failed policy blade and performed a hard browser refresh (`Ctrl + F5`) to clear cached session state.
  2. Relaunched the portal session in an InPrivate window to re-authenticate Entra ID token context.
  3. Successfully initialized the policy creation blade.

#### Issue 03: Disabled Dependent Policy Settings in Intune Compliance Configuration
* **Symptom:** The `Real-time protection` toggle in the Microsoft Defender compliance configuration panel was grayed out and unselectable.
* **Root Cause:** Microsoft Intune enforces parent-child setting dependencies. Sub-features under Defender require the main engine policy to be explicitly enabled first.
* **Resolution Path:**
  1. Set **Microsoft Defender Antimalware** to **Require**.
  2. Verified that **Real-time protection** and **Security intelligence up-to-date** toggles unlocked immediately.
  3. Enforced all child rules and successfully saved `POL - Windows 11 Security Baseline`.
---

## ⚡ Module 3: Programmatic Identity Lifecycle Management (PowerShell Automation)

### Step 1: Automated User Onboarding & Group Membership
* **Execution Goal:** Automate account provisioning for new hire **Alex Rivera**, enforce forced password reset on initial sign-in, and automatically map role-based access control (RBAC) to security groups.
* **Technical Concept:** Utilizing New-MgUser and New-MgGroupMember to eliminate manual administrative overhead during onboarding while maintaining zero-trust credential hygiene.
* **PowerShell Command Executed:**
  ```powershell
  $TenantDomain = (Get-MgDomain \vert{} Where-Object {$_.IsDefault} | Select-Object -ExpandProperty Id)

  $PasswordProfile = @{
      Password = "Password123!#Onboard2026"
      ForceChangePasswordNextSignIn = $true
  }

  $UserParams = @{
      DisplayName       = "Alex Rivera"
      GivenName         = "Alex"
      Surname           = "Rivera"
      UserPrincipalName = "alex.rivera@$TenantDomain"
      MailNickname      = "arivera"
      AccountEnabled    = $true
      PasswordProfile   = $PasswordProfile
      UsageLocation     = "US"
  }

  $NewUser = New-MgUser @UserParams

  $Group = Get-MgGroup -Filter "displayName eq 'GRP_SG_Remote_Workers'"
  if ($Group) {
      New-MgGroupMember -GroupId $Group.Id -DirectoryObjectId$NewUser.Id
  }

  
<br>
**Verification Screenshot:**
<br>
<img src="03-user-onboard-script.png" alt="Intune MAM App Protection Policy" width="750" />

<br>
<br>
<img src="03-User-onboard-proff.png" alt="Intune MAM App Protection Policy" width="750" />

<br>

### Step 2: Automated User Offboarding & Session Revocation
* **Execution Goal:** Rapidly isolate departing user Alex Rivera by disabling sign-in, revoking active client sessions, and stripping security group memberships to prevent unauthorized corporate access.
* **Technical Distinction (Disabling vs. Deleting):** Maintained the user object in Microsoft Entra ID with `AccountEnabled = $false` rather than deleting it. This ensures mailbox logs, file ownership, and audit trails remain accessible for eDiscovery while completely blocking sign-in capability.
* **Session Containment:** Executed `Revoke-MgUserSignInSession` to immediately invalidate active OAuth2 refresh tokens, forcing an instant sign-out across all cached web apps, desktop clients, and mobile sessions.

* **PowerShell Command Executed:**
  ```powershell
  $TargetUPN = "alex.rivera@$TenantDomain"
  $TargetUser = Get-MgUser -Filter "userPrincipalName eq '$TargetUPN'"

  if ($TargetUser) {
      # 1. Block account sign-in capability
      Update-MgUser -UserId $TargetUser.Id -AccountEnabled:$false

      # 2. Instantly revoke active OAuth refresh tokens / client sessions
      Revoke-MgUserSignInSession -UserId $TargetUser.Id | Out-Null

      # 3. Strip role-based security group access
      $Group = Get-MgGroup -Filter "displayName eq 'GRP_SG_Remote_Workers'"
      if ($Group) {
          Remove-MgGroupMemberByRef -GroupId $Group.Id -DirectoryObjectId$TargetUser.Id
      }
  }
  
**Verification Screenshot:**
<br>
<img src="04-user-offboard-script.png" alt="Intune MAM App Protection Policy" width="750" />

<br>
<br>
<img src="04-offboard-proof.png" alt="Intune MAM App Protection Policy" width="750" />

<br>

### Step 3: Enterprise CSV Bulk User Provisioning

* **Execution Goal:** Automate enterprise-scale onboarding by parsing an HR CSV data feed (`NewUsers.csv`), dynamically generating tenant-qualified UPNs, initializing temporary credentials, and mapping users into security groups.
* **Technical Concept:** Leveraging `Import-Csv` and a `ForEach-Object` pipeline to automate multi-account creation safely, ensuring consistent metadata application across departments.
* **PowerShell Command Executed:**

```powershell
$CsvPath = "C:\Users\ramya\NewUsers.csv"
$TenantDomain = (Get-MgDomain | Where-Object {$_.IsDefault} \vert{} Select-Object -ExpandProperty Id)$Group = Get-MgGroup -Filter "displayName eq 'GRP_SG_Remote_Workers'"

Import-Csv -Path $CsvPath \vert{} ForEach-Object {$PasswordProfile = @{
        Password = "Password123!#Onboard2026"
        ForceChangePasswordNextSignIn = $true
    }

    $UserParams = @{
        DisplayName       = $_.DisplayName
        GivenName         = $_.GivenName
        Surname           = $_.Surname
        UserPrincipalName = "$($_.UserPrincipalName)@$TenantDomain"
        MailNickname      = $_.MailNickname
        Department        = $_.Department
        UsageLocation     = $_.UsageLocation
        AccountEnabled    = $true
        PasswordProfile   = $PasswordProfile
    }

    $NewUser = New-MgUser @UserParams

    if ($Group) {
        New-MgGroupMember -GroupId $Group.Id -DirectoryObjectId$NewUser.Id
    }
}

** Verification Screenshot: **
<br>
<img src="05-bulk-user-onboard-script.png" alt="Intune MAM App Protection Policy" width="750" />

<br>
<br>
<img src="05-bulk-user-onboard-proof.png" alt="Intune MAM App Protection Policy" width="750" />

<br>
  
