# Claude Digital Assistant Framework
## High-Level Plan for Unified Multi-Instance Access

---

## The Big Picture: What You're Building

Think of this like setting up a **central nervous system** for your digital life, where Claude is the brain that can access your information from anywhere — your Mac, your phone, or a web browser.

**The Goal:** No matter which Claude interface you use, it can access your files, calendars, and ongoing projects.

---

## Understanding the Current Reality

### The Challenge
Right now, different Claude products are **separate systems** that don't automatically talk to each other:

| Interface | What It Can Access Natively |
|-----------|----------------------------|
| **DESKTOP** | |
| Claude.ai (web browser) | Cloud-based conversations, Projects, Memory |
| Claude Desktop app | Same as web + local MCP extensions |
| Claude Code (terminal CLI) | Local files in your project folder |
| Claude Code (VS Code extension) | Same as CLI with GUI in your IDE |
| **CLOUD/BROWSER** | |
| Claude Code (browser "Code" tab) | GitHub repos via cloud infrastructure |
| **MOBILE (iOS)** | |
| Claude iOS Chat | Conversations synced with Claude.ai + iOS app integrations |
| Claude Code on iOS | GitHub repos via cloud (same as browser Code tab) |

### Breaking Down iOS Options

The Claude iOS app actually has **two different modes** you can use:

#### 1. Claude Chat (iOS) — General Assistant
This is the main chat interface in the Claude iOS app.

**What it can do:**
- Have conversations (synced with Claude.ai web)
- Access your Claude.ai Projects
- **Native iOS integrations:**
  - Draft & send Messages (iMessage, WhatsApp, etc.)
  - Compose emails (Mail, Gmail, Outlook)
  - Create/read Calendar events
  - Set Reminders
  - Access your Location for recommendations
  - Show places on Maps
- Voice mode (talk to Claude)
- Analyze photos with your camera
- Siri shortcuts ("Ask Claude...")
- Widgets for quick access

**What it CAN'T do:**
- Access files on your Mac directly
- Run code or terminal commands
- Edit files in repositories

#### 2. Claude Code (iOS) — Coding Assistant
This is accessed via the **"Code" tab** in the Claude iOS app. Launched November 2025 as a research preview.

**What it can do:**
- Connect to your GitHub repositories
- Run coding tasks on Anthropic's cloud servers
- Monitor tasks while on the go
- Kick off parallel coding jobs
- Continue work started on web

**What it CAN'T do (yet — it's in research preview):**
- Show inline code diffs (like desktop does)
- Access GitLab or Bitbucket (GitHub only for now)
- Replace a full desktop IDE for complex work
- Start a task on your computer terminal and continue it on phone

**Who can use it:**
- Requires Pro, Max, Team, or Enterprise plan
- iOS only (not on Android yet)

### The Solution: A Shared Hub
We'll create a **central folder structure** on your Mac that acts as the "shared brain" — and connect all Claude instances to it using different methods.

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        YOUR MAC (Local Machine)                         │
│                                                                         │
│   ┌─────────────────────────────────────────────────────────────────┐  │
│   │              📁 CENTRAL HUB: ~/Claude-Assistant/                 │  │
│   │                                                                  │  │
│   │   📁 knowledge/          ← Reference docs, notes, guides        │  │
│   │   📁 projects/           ← Individual project folders           │  │
│   │   📁 context/            ← CLAUDE.md files, shared context      │  │
│   │   📁 templates/          ← Reusable prompts, workflows          │  │
│   │   📁 sync/               ← Files synced to iCloud for phone     │  │
│   │                                                                  │  │
│   └─────────────────────────────────────────────────────────────────┘  │
│                          │                                              │
│            ┌─────────────┴─────────────┐                               │
│            │                           │                                │
│            ▼                           ▼                                │
│   ┌────────────────┐         ┌────────────────┐                        │
│   │ Claude Code    │         │ Claude Desktop │                        │
│   │ (Terminal CLI) │         │    App         │                        │
│   │                │         │                │                        │
│   │ Direct file    │         │ MCP Extension: │                        │
│   │ access via     │         │ "Filesystem"   │                        │
│   │ --add-dir flag │         │                │                        │
│   └────────────────┘         └────────────────┘                        │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
            ┌───────────────────────┼───────────────────────┐
            │                       │                       │
            ▼                       ▼                       ▼
    ┌──────────────┐       ┌──────────────┐       ┌────────────────────┐
    │ Claude.ai    │       │ Claude Code  │       │    Claude iOS      │
    │ (Web)        │       │ (Browser)    │       │                    │
    │              │       │              │       │  ┌──────────────┐  │
    │ Projects +   │       │ "Code" tab   │       │  │ Chat Mode    │  │
    │ Memory       │       │              │       │  │ • Messages   │  │
    │              │       │ Points to    │       │  │ • Calendar   │  │
    │ ↕ Google     │       │ same GitHub  │       │  │ • Reminders  │  │
    │   Drive MCP  │       │ repos        │       │  │ • Voice      │  │
    │              │       │              │       │  └──────────────┘  │
    │              │       │      ↕       │       │         +          │
    │              │       │   Synced     │       │  ┌──────────────┐  │
    │              │       │              │       │  │ Code Mode    │  │
    │              │       │              │◄──────┼──│ "Code" tab   │  │
    │              │       │              │       │  │ GitHub repos │  │
    └──────────────┘       └──────────────┘       │  └──────────────┘  │
            │                       │             └────────────────────┘
            └───────────────────────┼───────────────────────┘
                                    │
                                    ▼
                    ┌──────────────────────────────────┐
                    │   ☁️  CLOUD SYNC LAYER            │
                    │                                  │
                    │   • iCloud Drive (Mac ↔ iPhone)  │
                    │   • Google Drive (via MCP)       │
                    │   • GitHub (code projects)       │
                    │   • Apple Calendar (via iOS)     │
                    │   • Anthropic Cloud (Code tasks) │
                    └──────────────────────────────────┘
```

---

## How Each Claude Interface Connects

### 1. Claude Code (Terminal) — DIRECT FILE ACCESS
- **How:** Run `claude --add-dir ~/Claude-Assistant` when starting a session
- **What it can do:** Read/write any file in that folder
- **Best for:** Coding projects, creating documents, organizing files

### 2. Claude Desktop App — MCP EXTENSIONS
- **How:** Install the "Filesystem" desktop extension (Settings → Extensions)
- **What it can do:** Read files from folders you grant access to
- **Best for:** Conversations where you want to reference local docs

### 3. Claude.ai (Web Browser) — CLOUD CONNECTORS
- **How:** Connect Google Drive MCP, upload files to Projects
- **What it can do:** Access cloud files, use Projects for context
- **Best for:** Research, learning conversations, planning

### 4. Claude Code (Browser "Code" Tab) — GITHUB + CLOUD
- **How:** Connected to your GitHub (you already did this with "life-os")
- **What it can do:** Work on code repositories, runs on Anthropic's cloud
- **Best for:** Coding when you don't want to use terminal, parallel tasks

### 5. Claude iOS Chat — NATIVE iOS FEATURES
- **How:** Download Claude app from App Store, sign in with your account
- **What it can do:** 
  - Conversations (synced with Claude.ai)
  - Draft messages, emails
  - Create calendar events & reminders
  - Voice conversations
  - Photo analysis
  - Location-based recommendations
- **Best for:** On-the-go tasks, quick questions, mobile actions

### 6. Claude Code on iOS — MOBILE CODING (Research Preview)
- **How:** Open the Claude iOS app → tap the "Code" tab
- **What it can do:**
  - Connect GitHub repositories
  - Kick off coding tasks that run in Anthropic's cloud
  - Monitor progress on tasks started from web
  - Work on multiple tasks in parallel
- **Limitations (still in preview):**
  - No inline code diffs yet
  - GitHub only (no GitLab/Bitbucket)
  - Can't continue a terminal session from your Mac
  - Not as full-featured as desktop
- **Best for:** Monitoring tasks, quick fixes, on-the-go coding

---

## Recommended Folder Structure

```
~/Claude-Assistant/
│
├── CLAUDE.md                    ← Master context file (who you are, your goals)
│
├── 📁 knowledge/
│   ├── personal-info.md         ← Preferences, communication style
│   ├── tools-and-accounts.md    ← What services you use
│   └── learning-goals.md        ← What you're working to learn
│
├── 📁 projects/
│   ├── life-os/                 ← Your existing GitHub project
│   ├── lucavo-cep/              ← Future project folders
│   └── learning-claude/         ← Notes from your Claude learning journey
│
├── 📁 context/
│   ├── current-priorities.md    ← What you're focused on right now
│   ├── decisions-log.md         ← Major decisions and why
│   └── open-questions.md        ← Things you're still figuring out
│
├── 📁 templates/
│   ├── new-project-setup.md     ← How to start new projects
│   └── daily-planning.md        ← Daily planning prompts
│
└── 📁 sync/                     ← Folder synced to iCloud for phone access
    ├── quick-notes.md           ← Capture ideas on the go
    └── phone-tasks.md           ← Tasks to do from phone
```

---

## Phase 1: Foundation Setup (Start Here)

### Step 1: Create the Central Hub Folder
```bash
mkdir -p ~/Claude-Assistant/{knowledge,projects,context,templates,sync}
```

### Step 2: Create Your Master CLAUDE.md
This file tells Claude who you are across ALL sessions:

```markdown
# Vincent's Digital Assistant Context

## About Me
- Learning: AI-assisted coding, Claude products, computer science fundamentals
- Skill level: Beginner (explain things simply)
- Current projects: life-os (GitHub), learning Claude Code

## Communication Preferences
- Use simple language, avoid jargon
- Define technical terms when you use them
- Ask if I understand before moving on

## Current Priorities
[Update this weekly]
1. Setting up Claude as a unified digital assistant
2. Learning Claude Code workflows
3. ...

## Tools I Use
- Mac desktop
- iPhone
- GitHub account (connected to Claude Code)
- Craft app (you have MCP connected)
```

### Step 3: Set Up Cloud Sync (For Phone Access)
- Put the `~/Claude-Assistant/sync/` folder in iCloud Drive
- Access it from your iPhone through Files app
- Claude iOS can't read these files directly, BUT you can copy/paste content into conversations

---

## Phase 2: Connect the Interfaces

### Claude Code (Terminal)
```bash
# Start Claude Code with access to your assistant folder
cd ~/Claude-Assistant
claude

# OR from any location:
claude --add-dir ~/Claude-Assistant
```

### Claude Desktop App
1. Open Claude Desktop → Settings → Extensions
2. Install "Filesystem" extension
3. Grant access to `~/Claude-Assistant/`

### Claude.ai (Web)
1. Create a Project called "Digital Assistant Hub"
2. Upload key files from your knowledge/ folder
3. Connect Google Drive MCP if you want cloud doc access

### Claude Code (Browser)
1. Go to claude.ai → Code tab
2. Connect your GitHub account (you already did this)
3. Select a repository to work on

### Claude iOS (Chat Mode)
1. Download Claude app from App Store
2. Sign in with your Anthropic account
3. Your conversations sync automatically
4. Grant permissions for Calendar, Reminders, etc. as needed

### Claude Code on iOS
1. Open Claude iOS app
2. Tap the "Code" tab at the bottom
3. Connect GitHub (same account as web)
4. Select a repository and start a task

---

## Phase 3: Future Enhancements

Once the foundation is working, you can add:

| Enhancement | What It Does |
|-------------|--------------|
| **Calendar MCP** | Claude can read/write your calendar |
| **Obsidian MCP** | If you use Obsidian for notes |
| **Custom MCP Server** | Build your own integrations |
| **Automation Scripts** | Auto-sync files between services |

---

## What This DOESN'T Do (Yet)

Being honest about current limitations:

1. **No automatic sync between Claude instances** — You can't have Claude Code terminal "remember" what you discussed in Claude.ai web
2. **No central conversation history** — Each interface has its own conversations
3. **File access varies by interface** — Some can only read, not write
4. **Mobile Claude Code is limited** — iOS Claude Code can't access Mac files directly, only GitHub repos via cloud
5. **Can't transfer sessions** — You can't start a task on terminal Claude Code and continue it on mobile (yet)

**The workaround:** Use your central folder + CLAUDE.md as the "source of truth" that you point Claude to at the start of sessions.

---

## Quick Reference: Which Claude for What?

| Task | Best Interface | Why |
|------|---------------|-----|
| Writing code (focused work) | Claude Code (terminal) | Direct file access, full features |
| Writing code (on the go) | Claude Code (iOS or browser) | Cloud-based, can monitor from anywhere |
| Research & learning | Claude.ai (web) | Research mode, web search, Projects |
| Quick questions on the go | Claude iOS Chat | Convenient, voice mode |
| Organizing files | Claude Code or Desktop | File system access |
| Calendar/reminders | Claude iOS Chat | Native iOS integration |
| Drafting messages/emails | Claude iOS Chat | Direct integration with apps |
| Long planning sessions | Claude.ai Projects | Memory, file uploads |
| Parallel coding tasks | Claude Code (browser or iOS) | Run multiple tasks in cloud |

---

## Next Steps

1. ✅ Create the folder structure
2. ✅ Write your master CLAUDE.md
3. ⬜ Test Claude Code with `--add-dir`
4. ⬜ Install Filesystem extension on Claude Desktop
5. ⬜ Create a "Digital Assistant Hub" project on Claude.ai
6. ⬜ Try Claude Code on iOS (Code tab)

---

*This document was created during our December 31, 2025 session setting up your digital assistant framework.*
*Updated to include Claude Code on iOS breakdown.*
