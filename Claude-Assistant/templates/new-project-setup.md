# New Project Setup Template

Use this checklist when starting a new project with Claude.

---

## Step 1: Create the Project Folder
```bash
mkdir -p ~/Claude-Assistant/projects/[project-name]
cd ~/Claude-Assistant/projects/[project-name]
```

## Step 2: Initialize Git (if it's a coding project)
```bash
git init
```

## Step 3: Create a Project CLAUDE.md
Create a file called `CLAUDE.md` in the project folder with:

```markdown
# [Project Name]

## What This Project Is
[One paragraph description]

## Goals
1. [Primary goal]
2. [Secondary goal]

## Tech Stack (if applicable)
- [Language/framework]
- [Tools]

## Current Status
[What phase is this project in?]

## Important Files
- [List key files Claude should know about]

## Notes for Claude
- [Any project-specific instructions]
```

## Step 4: Connect to Claude Code
```bash
# Navigate to your project
cd ~/Claude-Assistant/projects/[project-name]

# Start Claude Code
claude
```

## Step 5: (Optional) Create GitHub Repository
If you want to use Claude Code in the browser:
1. Create a new repo on GitHub
2. Push your local project to it
3. Access via the Code tab in claude.ai

---

*This template ensures every project has the context Claude needs to help effectively.*
