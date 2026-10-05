# Event Analysis Framework

One of the most important skills for an analyst is understanding the difference between:

- What is known
- What is unknown

## Example Event

### Known Information

| Field | Observation |
|---------|------------|
| Time | 2026-07-12 14:32:55 |
| User | VOSTRO |
| Process | powershell.exe |
| Parent Process | cmd.exe |
| Current Directory | Documents |
| Command Line | cmd.exe |
| Parent Command Line | powershell.exe |

---

## Unknown Information

### User Intent

We cannot determine whether the user intentionally executed the process.

### Process Outcome

We do not know what happened after execution.

### Full Process Chain

The event does not reveal future child processes.

### Network Activity

No network information is available.

---

## Key Lesson

A single event rarely tells the complete story.

Analysts must identify both available evidence and missing evidence.

# Process Chain Observation

## Observed Sequence

```text
WINWORD.EXE

↓

powershell.exe

↓

cmd.exe

↓

notepad.exe
```

---

## Initial Observations

The observed sequence demonstrates how a process chain can be reconstructed from Sysmon telemetry.

Each process provides context for the next process in the sequence.

Without parent-child relationships, the execution of these processes would appear unrelated.

---

## Investigation Value

Medium

The sequence is useful for understanding process lineage but does not automatically indicate malicious activity.

# Investigation Challenge

## Timeline

```text
explorer.exe

↓

cmd.exe /c inventory.bat

↓

powershell.exe -ExecutionPolicy Bypass

↓

Network Connection

↓

msedge.exe

↓

excel.exe

↓

notepad.exe
```

---

# Simple View

```text
inventory.bat

↓

cmd.exe

↓

powershell.exe

↓

Network Connection
```

---

# Hypothesis

## Hypothesis 1

The user intentionally executed a batch script that launched PowerShell and connected to an internal system.

The observed behavior may be part of an approved administrative workflow.

---

## Hypothesis 2

The batch file may contain commands that automate administrative tasks and communicate with another internal system.

---

# Evidence Supporting

- inventory.bat was executed.
- PowerShell was launched from cmd.exe.
- ExecutionPolicy Bypass was used.
- PowerShell established a network connection.

---

# Evidence Against

- Internal IP addresses are frequently used in legitimate enterprise workflows.
- ExecutionPolicy Bypass is not inherently malicious.
- Other observed applications may be unrelated to the process chain.

---

# Confidence Level

Low to Medium

The available evidence suggests activity worth investigating but does not support a definitive conclusion.

---

# Additional Evidence Required

- Contents of inventory.bat
- PowerShell script content
- Historical behavior of the user
- Destination host role
- Network communication details

# Detection Observations

## Event 1

```text
powershell.exe -ExecutionPolicy Bypass
```

### Detection Value

Medium

### Why?

The command may be used for:

- legitimate automation
- administrative scripts
- software deployment
- malicious script execution

The command alone is not sufficient evidence of malicious behavior.

---

## Event 2

```text
Network Connection
```

### Detection Value

Medium

### Why?

Network activity often provides valuable investigative context.

Connections become more interesting when correlated with:

- unusual process execution
- suspicious command lines
- external destinations
- privileged sessions

---

## Key Lesson

Single events rarely justify a high-confidence alert.

Correlated behavior is significantly more valuable than isolated indicators.

# Lessons Learned

## 1. Avoid Jumping to Conclusions

Suspicious does not mean malicious.

Evidence must be collected before making a determination.

---

## 2. Context Matters

The same process may be benign in one environment and suspicious in another.

---

## 3. Missing Information Matters

Analysts should explicitly identify:

- What is known
- What is unknown
- What evidence is still required

---

## 4. Process Chains Are More Valuable Than Single Events

Investigating related events provides better context than analyzing isolated logs.

---

## 5. Confidence Levels Should Reflect Evidence

Confidence should increase only when supporting evidence is available.

Analysts should be comfortable saying:

"More evidence is required."