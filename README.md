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
