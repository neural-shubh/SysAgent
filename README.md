# SysAgent

**An AI-assisted Windows system guardian that monitors your machine, finds what is consuming space, identifies potentially unnecessary data, and helps you clean it safely.**

SysAgent started from a simple idea:

> What if your computer could tell you what is happening to the system instead of making you manually search for it?

Rather than being just another disk-cleaning utility, SysAgent combines **system monitoring, storage analysis, automated cleanup, human approval, quarantine, and an AI-assisted decision layer** into one system agent.

## What It Does

SysAgent runs in the background and periodically checks different parts of your system.

It can:

* Monitor disk usage and detect low-storage conditions
* Find temporary files, caches, crash dumps, and other regenerable junk
* Detect large files that have been unused for a long time
* Find duplicate files using hashing
* Identify stale developer artifacts such as `node_modules`, virtual environments, build folders, and caches
* Monitor CPU, RAM, battery, and basic system health
* Check installed applications and identify large applications
* Inspect Windows startup programs
* Check available software updates through `winget`
* Keep a history of scans and actions
* Notify the user when something important happens
* Provide a dashboard for monitoring and approving actions

The important part is that SysAgent **doesn't treat every file as equally safe to remove.**

---

## Safety First

File cleanup can become dangerous very quickly if an automated system is given unrestricted access.

SysAgent therefore uses a tiered approach.

### Tier 1 — Safe / Auto-Quarantine

These are primarily regenerable files such as:

* Temporary files
* Safe application caches
* Crash dumps
* Python caches
* Test caches
* Other known disposable data

Instead of immediately deleting them, SysAgent moves them into a **quarantine area**.

### Tier 2 — Human Approval

Potentially important files are not automatically removed.

Examples include:

* Large unused files
* Duplicate files
* Developer project artifacts
* Applications
* Other files that require context before removal

These appear in the dashboard's **Approvals** section, where the user can approve or reject the action.

### Protected Paths

SysAgent also contains hardcoded protections for important Windows locations and system files.

Examples include:

* `C:\Windows`
* `C:\Program Files`
* `C:\ProgramData\Microsoft\Windows`
* `C:\Recovery`
* `C:\Boot`
* `pagefile.sys`
* `hiberfil.sys`
* `swapfile.sys`
* `ntuser.dat`

These protections are enforced by the safety layer rather than being left to the AI.

---

## AI-Assisted Classification

SysAgent includes an optional LLM layer for situations where deterministic rules aren't enough.

The LLM acts as a **consultant**, not the final authority.

The workflow is roughly:

```text
File / Folder
     │
     ▼
Safety checks
     │
     ├── Protec
```
