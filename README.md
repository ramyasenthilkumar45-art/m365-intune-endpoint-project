# Enterprise M365, Intune Endpoint & Hybrid Administration Lab

## 📌 Executive Summary
A comprehensive enterprise lab environment built to demonstrate hands-on expertise in Microsoft 365 administration, Intune Modern Management, Hybrid Active Directory provisioning, and IT service desk workflows.

## 🏗️ Core Architecture & Skills
* **Identity & Access Management:** Microsoft Entra ID (Azure AD), Hybrid Active Directory, RBAC, Conditional Access (MFA Enforce), Group-Based Licensing.
* **Endpoint Management (MDM/MAM):** Microsoft Intune configuration, Compliance Policies, App Protection, and Windows Autopilot profiles.
* **Automation & Scripting:** PowerShell scripts for user onboarding/offboarding and system monitoring.
* **IT Infrastructure & Virtualization:** Windows Server administration, Hyper-V, networking fundamentals, and backup validation.

## 🎫 Helpdesk & 2nd-Level Support Scenarios
* **Scenario 1 (Onboarding):** Provisioning new users via group-based licensing and automated MFA onboarding.
* **Scenario 2 (Endpoint Compliance):** Resolving BitLocker and firewall non-compliance issues reported in Intune.
* **Scenario 3 (Access Troubleshooting):** Diagnosing and resolving Conditional Access block events using Sign-in Logs.

## 🛠️ Real-World Troubleshooting Log

### Issue 01: Conditional Access Policy Creation Blocked by Security Defaults
* **Symptom:** Entra ID threw error: `Security defaults must be disabled to enable Conditional Access policy.`
* **Root Cause:** Microsoft Security Defaults enforce basic baseline security and cannot coexist with custom Conditional Access rules.
* **Resolution:** Navigated to **Entra ID** > **Properties** > **Manage Security defaults**, set to **Disabled**, and selected *Using Conditional Access*.
* **Verification:** Policy `CAP - Enforce MFA for Remote Workers` created successfully.

![Security Defaults Conflict Error](01a-security-defaults-error.png)
![Conditional Access Policy Configured](01-conditional-access.png)
