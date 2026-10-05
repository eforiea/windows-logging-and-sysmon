# Windows Account Management Events

## Event ID 4720

### Description

A new local user account was created.

### Observed Event

| Field | Value |
|--------|-------|
| SubjectUserName | USER |
| TargetUserName | BOB |
| Time | 2026-07-09 08:47:13 |
| Computer | DESKTOP-15D8NF9 |

---

## Event ID 4724

### Description

An attempt was made to reset an account password.

### Observed Event

| Field | Value |
|--------|-------|
| SubjectUserName | USER |
| TargetUserName | BOB |

---

## Event ID 4732

### Description

A member was added to a local Administrators group.

### Observed Event

| Field | Value |
|--------|-------|
| SubjectUserName | USER |
| Group | Administrators |

---

## Event ID 4726

### Description

A user account was deleted.

### Observed Event

| Field | Value |
|--------|-------|
| SubjectUserName | USER |
| TargetUserName | BOB |

# Account Management Timeline

## Scenario

```text
09:10

4720
User Created

↓

09:12

4732
Added to Administrators

↓

09:13

4624
Successful Logon

↓

09:13

4672
Special Privileges Assigned
```

---

## Initial Analysis

The sequence shows:

1. A new user account was created.
2. The account received administrative privileges.
3. The account successfully authenticated.
4. The session received elevated privileges.

The sequence deserves investigation because it represents a high-impact administrative workflow.

However, available evidence alone is insufficient to determine whether the activity is legitimate or malicious.

# Investigation Case Study

## Timeline

```text
01:42

4720
backup_admin

↓

01:43

4732
Administrators

↓

01:44

4624
Logon Type 10

↓

01:45

4672

↓

01:47

powershell.exe
```

---

# Simple View

```text
User Account Created

↓

Added to Administrators

↓

Remote Logon

↓

Elevated Session

↓

PowerShell Execution
```

---

# Hypothesis

## Hypothesis 1

A system administrator may have created a temporary administrative account for maintenance or operational purposes.

## Hypothesis 2

An attacker may have created a new privileged account to maintain long-term administrative access after obtaining initial access.

---

# Evidence Supporting

- A new account was created.
- Administrative privileges were assigned shortly afterward.
- The account authenticated remotely.
- A privileged session was established.
- PowerShell execution followed.

---

# Evidence Against

- The activity may have been part of normal administrative operations.
- The account could have been created for another administrator or support engineer.

---

# Confidence Level

Medium

The sequence is suspicious and should be investigated, but available evidence is insufficient to conclude malicious intent.

---

# Additional Evidence Required

- Who created the account?
- Was the activity approved?
- Source IP of the remote session
- Historical administrator activity
- Commands executed inside PowerShell
- Network activity after authentication

# Detection Logic

## Individual Alerts

### Rule 1

Trigger a Low severity alert when:

- A new local account is created.

---

### Rule 2

Trigger a Medium severity alert when:

- A newly created account is added to the local Administrators group.

---

### Rule 3

Trigger a Medium severity alert when:

- A newly created administrative account performs its first successful logon.

---

### Rule 4

Trigger a Low severity alert when:

- PowerShell is executed shortly after the first privileged logon.

---

## Correlation Rule

Generate a High severity alert when all of the following occur within a short time window:

```text
User Created

↓

Added to Administrators

↓

Successful Logon

↓

Special Privileges Assigned

↓

PowerShell Execution
```

---

## Potential False Positives

- New employee onboarding
- IT maintenance
- Helpdesk account creation
- Temporary administrator accounts

---

## Suggested Investigation Steps

- Verify the account owner.
- Confirm change approval.
- Review remote logon source.
- Inspect PowerShell activity.
- Correlate with Sysmon process creation events.

