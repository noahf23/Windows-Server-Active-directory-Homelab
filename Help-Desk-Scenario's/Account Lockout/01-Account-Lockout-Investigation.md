# Scenario 01: Active Directory Account Lockout Investigation

**Environment:** Windows Server 2025, Active Directory Domain Services, PowerShell  
**Domain:** ad.findlayinc.test  
**Category:** Identity and Access Management  
**Difficulty:** Beginner  
**Status:** Initial investigation completed; full lockout simulation pending

## 1. Problem Description

An employee reports being unable to sign in to their workstation because their Active Directory account has been locked.

The employee explains that the issue started shortly after changing their password. Although IT has previously unlocked the account, the problem continues to occur.

**Objective:** Determine whether the account is locked, investigate potential causes, and identify the appropriate resolution without unnecessarily resetting the employee's password.

## 2. Initial Troubleshooting

I began by gathering information from the employee rather than immediately unlocking the account.

Questions included:

- When were you last able to sign in successfully?
- When did the issue first occur?
- Have you recently changed your password?
- Are you entering the correct password?
- Is Caps Lock enabled?
- Do you use your work account on any other devices, such as a mobile phone?

During the simulated troubleshooting conversation, the employee reported recently changing their password and having an Outlook application on their phone that continued requesting authentication.

This suggested that outdated saved credentials could be contributing to repeated failed login attempts.

## 3. Active Directory Investigation

I accessed my Windows Server domain controller and opened **Active Directory Users and Computers (ADUC)**.

I located a test employee account and opened its properties to inspect the account settings.

**Navigation:**

Active Directory Users and Computers → Employee Account → Properties → Account

I located the account unlock option and reviewed the account settings.

## 4. PowerShell Verification

To verify the account's lockout status, I opened PowerShell and executed:

```powershell
Get-ADUser -Identity s.smith -Properties LockedOut | Select-Object Name, LockedOut
```

**Result:**

```text
Name           LockedOut
----           ---------
Solana Smith   False
```

The `LockedOut` property returned `False`, confirming that the test account was not locked at the time of investigation.

**Understanding the results:**

- `True` — The account is currently locked.
- `False` — The account is not currently locked.

This demonstrated how PowerShell can be used to verify Active Directory account status without relying solely on the graphical interface.

## 5. Suspected Root Cause

In the simulated employee scenario, the likely cause was an outdated password stored on a mobile device.

After changing their Active Directory password, the employee had not updated the credentials used by their phone's Outlook application.

Repeated authentication attempts using an outdated password can trigger an account lockout when the domain's lockout threshold is reached.

**Important:** This was a suspected cause based on the simulated conversation, not a root cause confirmed through server logs.

## 6. Proposed Resolution

The appropriate troubleshooting process would be:

1. Verify the employee's identity according to company procedures.
2. Investigate the account lockout and review relevant authentication activity.
3. Identify and correct outdated saved credentials on the employee's device.
4. Unlock the account if authorized and appropriate.
5. Ask the employee to sign in using their current password.
6. Verify access to required applications.
7. Monitor for repeated account lockouts.
8. Document the actions and outcome in the support ticket.

## 7. Tools Used

- Windows Server 2025
- Active Directory Users and Computers
- Windows PowerShell
- Active Directory PowerShell module

## 8. Skills Demonstrated

- Gathering information from end users
- Troubleshooting authentication issues
- Navigating Active Directory
- Inspecting account properties
- Using PowerShell to check account status
- Forming hypotheses based on symptoms
- Understanding account lockout policies
- Documenting troubleshooting procedures

## 9. Lessons Learned

An account lockout is a symptom, not necessarily the underlying problem.

Simply unlocking an account may temporarily restore access without preventing the issue from recurring.

Effective troubleshooting involves gathering information, checking account status, investigating possible causes, and verifying that corrective actions resolve the issue.

I also learned that Active Directory administrators cannot directly view users' existing passwords, but can inspect account properties and authentication-related information.

## 10. Next Steps

- Simulate a real account lockout in the isolated homelab.
- Use Windows Event Viewer to investigate Security Event ID 4740.
- Identify the computer associated with a lockout.
- Perform and document an authorized account unlock.
- Verify successful authentication after remediation.

---

**Lab note:** The PowerShell account-status check was performed in my homelab. The employee interview, mobile-device issue, and proposed resolution were part of a guided troubleshooting simulation. A real account lockout and confirmed root-cause investigation have not yet been completed.
