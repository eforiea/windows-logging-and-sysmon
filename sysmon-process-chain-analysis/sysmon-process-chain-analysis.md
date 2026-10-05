# Process Baseline

Understanding normal parent-child relationships is essential before identifying suspicious behavior.

## Observed Examples

| Process | Parent Process | Initial Assessment |
|----------|---------------|-------------------|
| PowerShell | Program | Expected |
| CMD | Explorer | Expected |
| CMD | Microsoft Word | Unusual |

---

## Observations

Most command-line tools are normally launched from Windows Explorer, Terminal, scheduled tasks, or management tools.

Execution of `cmd.exe` directly from Microsoft Word is uncommon and deserves additional investigation.

This observation alone is not sufficient to classify the activity as malicious.

# Command Line Analysis

## Example

```text
cmd.exe /k ipconfig
```

---

## Why CommandLine Matters

The executable name only identifies which process started.

The command line provides additional context by revealing:

- execution parameters
- user intent
- administrative actions
- automation behavior

Without the command line, an analyst cannot accurately determine what the process attempted to do.

---

## Investigation Value

High

CommandLine is one of the most valuable fields for process investigations.

# Investigation Case Study

## Scenario

```text
WINWORD.EXE

↓

PowerShell

↓

rundll32.exe

↓

Network Connection

↓

Notepad
```

---

# Simple View

```text
Word

↓

PowerShell

↓

rundll32

↓

External Connection

↓

Notepad
```

---

# Hypothesis

## Hypothesis 1

A Microsoft Word document may have triggered PowerShell execution through embedded automation or scripting. The resulting process chain eventually established a network connection.

## Hypothesis 2

The observed behavior may represent a legitimate document automation workflow where PowerShell was intentionally used to perform administrative tasks.

The execution of Notepad appears independent and cannot currently be linked to the previous process chain.

---

# Evidence Supporting

- PowerShell was launched by Microsoft Word.
- rundll32.exe was created by PowerShell.
- The child process established a network connection.

---

# Evidence Against

- The network destination may be trusted.
- Document automation may be expected in the environment.
- No direct evidence links Notepad to the observed process chain.

---

# Confidence Level

Medium

The behavior deserves investigation because of the unusual process lineage, but the available evidence is insufficient to conclude malicious activity.

---

# Additional Evidence Required

- PowerShell CommandLine
- Network destination reputation
- Parent document origin
- Macro configuration
- Child process activity

# Detection Logic

## Candidate Detection

Generate a High severity alert when:

```text
WINWORD.EXE

↓

PowerShell

↓

Network Connection
```

occurs within a short period.

---

## Confidence Increases When

- PowerShell uses encoded commands.
- rundll32.exe is spawned.
- The destination IP is untrusted.
- The user does not normally execute Office macros.

---

## Potential False Positives

- Administrative Office automation
- Enterprise document workflows
- Approved scripting solutions

---

## Recommended Investigation

- Review Office document origin.
- Examine macro settings.
- Inspect PowerShell command line.
- Verify network destination.
- Review additional Sysmon events.

# Lessons Learned

## Key Takeaways

### Parent Process Matters

The same executable can represent completely different behaviors depending on its parent process.

---

### CommandLine Provides Context

A process name alone is rarely sufficient for investigation.

Command-line arguments often explain the purpose of execution.

---

### Avoid Assumptions

A suspicious process chain is not proof of malicious activity.

Evidence should always be collected before drawing conclusions.

---

### Think in Chains

Instead of investigating isolated events, analysts should correlate multiple events into a behavioral sequence.

Behavior-based analysis provides significantly higher detection value than single-event analysis.