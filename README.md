# 🛡️ SysAgent

**An AI-assisted Windows system guardian that monitors your machine, finds what is consuming space, identifies potentially unnecessary data, and helps you clean it safely.**

> 💡 **The idea behind SysAgent**
>
> What if your computer could tell you what is happening to the system instead of making you manually search for it?

SysAgent started from that idea.

Rather than being just another disk-cleaning utility, SysAgent combines **system monitoring, storage analysis, automated cleanup, human approval, quarantine, and an AI-assisted decision layer** into one system agent.

---

## What It Does

SysAgent runs in the background and periodically checks different parts of your system.

It can:

* 💾 Monitor disk usage and detect low-storage conditions
* 🧹 Find temporary files, caches, crash dumps, and other regenerable junk
* 📦 Detect large files that have been unused for a long time
* 🔍 Find duplicate files using hashing
* 🛠️ Identify stale developer artifacts such as `node_modules`, virtual environments, build folders, and caches
* 📊 Monitor CPU, RAM, battery, and basic system health
* 📱 Check installed applications and identify large applications
* ⚙️ Inspect Windows startup programs
* 🔄 Check available software updates through `winget`
* 📝 Keep a history of scans and actions
* 🔔 Notify the user when something important happens
* 🖥️ Provide a dashboard for monitoring and approving actions

The important part is that SysAgent **doesn't treat every file as equally safe to remove.**

---

## 🛡️ Safety First

File cleanup can become dangerous very quickly if an automated system is given unrestricted access.

SysAgent therefore uses a **tiered safety approach**.

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

Potentially important files are **not automatically removed**.

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

```text
C:\Windows
C:\Program Files
C:\ProgramData\Microsoft\Windows
C:\Recovery
C:\Boot
pagefile.sys
hiberfil.sys
swapfile.sys
ntuser.dat
```

These protections are enforced by the **safety layer**, rather than being left to the AI.

---

##  AI-Assisted Classification

SysAgent includes an optional LLM layer for situations where deterministic rules aren't enough.

The LLM acts as a **consultant, not the final authority**.

The workflow is roughly:

```text
                File / Folder
                     │
                     ▼
                Safety checks
                     │
              ┌──────┴──────┐
              │             │
          Protected       Allowed
              │             │
           Reject            ▼
                       Known rule?
                          │
                    ┌─────┴─────┐
                    │           │
                   Yes          No
                    │           │
                    ▼           ▼
                 Decision    LLM analysis
                                │
                         ┌──────┴──────┐
                         │             │
                      Tier 1        Tier 2
                         │             │
                         ▼             ▼
                    Quarantine    User approval
```

This means the model doesn't get unrestricted control over your filesystem.

When the model is uncertain, SysAgent takes the **more conservative route** and asks for human approval.

---

## ⚙️ Install & Run

### 1. Clone the repository

```powershell
git clone <your-repo-url>
cd sysagent
```

### 2. Install dependencies

```powershell
pip install -r requirements.txt
```

### 3. Start the background agent

```powershell
python run_agent.py
```

Or use the headless launcher:

```powershell
start_agent.bat
```

### 4. Open the dashboard

```powershell
python -m streamlit run dashboard.py
```

Or:

```powershell
start_dashboard.bat
```

Then visit:

**http://localhost:8501**

> 🧪 **Safety by default:** SysAgent ships with `"dry_run": true` in `config.json`, so cleanup actions are simulated first. Disable it from the dashboard's **Settings** page once you're comfortable with the behaviour.

---

## 🎛️ Manual Commands

You can also trigger individual scans directly from the command line:

```powershell
python run_agent.py --scan-storage    # one-off storage analysis
python run_agent.py --scan-junk       # one-off junk cleanup
python run_agent.py --weekly          # large files, duplicates, dev artifacts, apps
python run_agent.py --health          # instant health report
```

---

## ⏱️ Default Schedule

| Task                                                       | Frequency                                          |
| ---------------------------------------------------------- | -------------------------------------------------- |
| 💾 Storage check                                           | Every 2 hours                                      |
| 🧹 Junk cleanup + updates + health + quarantine purge      | Daily                                              |
| 🔍 Large files / duplicates / dev cleanup / apps / startup | Weekly — Sunday                                    |
| ⚠️ Low-disk event trigger                                  | Immediate cleanup when free space < 10% or < 20 GB |

All timings and thresholds live in `config.json` and can be edited from the dashboard.

---

## Project Layout

```text
sysagent/
│
├── run_agent.py          # Entry point — daemon + manual scans
├── dashboard.py          # Streamlit UI
├── config.json           # Settings and thresholds
│
├── core/
│   ├── daemon             # Background scheduling
│   ├── db                 # SQLite persistence
│   ├── safety             # Safety checks + quarantine
│   └── notify             # System notifications
│
├── modules/
│   ├── storage            # Disk usage analysis
│   ├── junk               # Junk detection
│   ├── duplicates        # Duplicate detection
│   ├── largefiles        # Large/unused file detection
│   ├── devclean          # Developer artifact cleanup
│   ├── startup           # Startup application analysis
│   ├── uninstall         # Application analysis
│   ├── updates           # Software update checks
│   └── health            # System health monitoring
│
├── brain/
│   ├── LLM classification
│   └── Learned-rule cache
│
└── data/
    └── gitignored
        ├── agent.db
        ├── quarantine/
        └── logs/
```

---

## Requirements

* **Windows 10 / 11**
* **Python 3.11+**
* **winget** — for software update checks

---

## 🔭 What's Next?

SysAgent is currently focused on **understanding and safely managing the state of a Windows machine**.

Some directions I'm exploring for future versions:

* Natural-language commands for interacting with the system
* More event-based OS monitoring
* Smarter file and application usage analysis
* Better explanations for *why* storage changed
* Process and resource anomaly detection
* More advanced local AI models
* A dedicated desktop interface instead of Streamlit
* More granular user-defined cleanup policies

The long-term idea is to move beyond a traditional **PC cleaner** and build something closer to an **AI system assistant that understands what is happening on your computer.**

---
