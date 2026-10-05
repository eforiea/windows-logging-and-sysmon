# Windows Authentication Events

## Event ID 4624

### Description

A user successfully authenticated to the system.

### Observed Fields

| Field | Value |
|--------|-------|
| TargetUserName | BOB |
| LogonType | 7 |
| LogonProcess | User32 |
| Authentication Package | Negotiate |
| Source IP | 192.168.147.1 |
| Workstation | DESKTOP-15D8NF9 |

---

## Event ID 4625

### Description

A logon attempt failed.

### Observed Fields

| Field | Value |
|--------|-------|
| TargetUserName | USER |
| LogonType | 11 |
| LogonProcess | CredPro |
| Authentication Package | Negotiate |
| Source IP | ::1 |
| Workstation | DESKTOP-15D8NF9 |

---

## Event ID 4634

### Description

A logon session was terminated.

---

## Event ID 4672

### Description

Special privileges were assigned to a newly created logon session.

This event is important because it indicates that the session received elevated privileges. It does not necessarily mean that those privileges were used.

# Authentication Timeline Analysis

## Timeline

```text
06:49:56

4625
Failed Logon
User: BOB

↓

06:50:00

4624
Successful Logon
User: BOB

↓

06:51:30

4625
Failed Logon

↓

06:51:33

4625
Failed Logon

↓

06:51:38

4624
Successful Logon

↓

06:52:08

4647
User Initiated Logoff
```

---

## Initial Observations

- Multiple failed authentication attempts occurred before successful logons.
- The same user account was involved throughout the sequence.
- The session ended normally with a user-initiated logoff.

---

## Investigation Notes

Although repeated failed logons may indicate password mistakes, they can also represent password guessing attempts.

Additional evidence is required before classifying the activity as malicious.

# Investigation Case

## Scenario

```text
4625 × 25

↓

4624

↓

4672

↓

4688

powershell.exe
```

---

# Hypothesis

A brute-force attack is possible if the repeated failed authentication attempts originate from the same source IP or target the same account within a short period.

---

# Evidence Supporting

- Twenty-five failed authentication attempts were observed.
- A successful authentication occurred afterward.
- A privileged session was established.
- PowerShell execution followed the successful logon.

---

# Evidence Against

- The activity may have been performed by a legitimate administrator.
- A user may have repeatedly entered an incorrect password before successfully authenticating.

---

# Confidence Level

Medium

The observed sequence is suspicious but insufficient to conclude malicious activity without additional evidence.

---

# Additional Evidence Required

- Source IP consistency
- Time interval between failed attempts
- Whether the successful logon originated from the same source
- User activity after authentication
- Historical behavior of the account

---

# Detection Idea

Trigger a Low severity alert when:

- More than five failed logons occur
- Within one minute
- Against the same user or from the same IP

Escalate the alert if a successful logon follows shortly afterward.

# Case Study

## Timeline

```text
10:00

4625
administrator

↓

10:01

4625
admin

↓

10:02

4625
administrator

↓

10:03

4624
administrator
(Logon Type 10)

↓

10:04

4672

↓

10:05

cmd.exe

↓

10:06

powershell.exe
```

---

# Hypothesis

A remote user may have successfully authenticated after several failed attempts and subsequently executed administrative command-line tools.

---

# Evidence Supporting

- Authentication attempts originated from a public IP address.
- Multiple failed logons occurred before a successful authentication.
- The successful session received elevated privileges.
- Command Prompt and PowerShell were executed immediately afterward.

---

# Evidence Against

- The administrator may have mistyped the password several times.
- The activity may represent a legitimate remote administrative session.

---

# Confidence Level

Medium

The activity is suspicious and requires investigation, but available evidence does not conclusively indicate malicious behavior.

---

# Recommended Next Steps

- Verify whether the source IP is trusted.
- Review PowerShell command history.
- Examine child processes created by PowerShell.
- Investigate outbound network connections.
- Confirm whether the administrator initiated the session.