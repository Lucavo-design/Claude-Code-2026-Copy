# Mobile Claude Code Architecture Comparison

*Comparing Pete Sena's proven setup vs. cloud-first voice-enabled approach*

---

## The Two Approaches

### Option A: Pete Sena's Stack (Mac + SSH)
**Tailscale + Termius + tmux**

```
┌─────────────┐         ┌─────────────────────┐
│   iPhone    │──SSH───►│   Mac (always on)   │
│  (Termius)  │         │   Claude Code       │
└─────────────┘         │   in tmux session   │
      │                 └─────────────────────┘
      │
  Tailscale VPN
```

### Option B: Cloud-First with Voice
**Telegram Bot + GitHub Codespaces + Tailscale**

```
┌─────────────┐         ┌─────────────────────┐
│   iPhone    │──Bot───►│   Cloud (Codespaces)│
│  (Telegram) │         │   Claude Code       │
│  voice+text │         │   GitHub files      │
└─────────────┘         └─────────────────────┘
                               │
                               ▼ (when online)
                        ┌─────────────┐
                        │     Mac     │
                        │  Tailscale  │
                        └─────────────┘
```

---

## Side-by-Side Comparison

| Aspect | Pete's (Mac + SSH) | Cloud-First (Telegram) |
|--------|-------------------|------------------------|
| **Voice input** | ❌ None | ✅ Telegram voice msgs |
| **Works when Mac off** | ❌ No | ✅ Yes (Codespaces) |
| **Setup difficulty** | ✅ Easy (1-2 hrs) | ⚠️ Harder (10-20 hrs) |
| **Proven solution** | ✅ Yes, widely used | ❌ Custom build required |
| **Local file access** | ✅ Full filesystem | ⚠️ GitHub only (unless Mac online) |
| **Real-time response** | ✅ Guaranteed | ⚠️ Depends on bridge |
| **Cost** | ✅ Free tiers | ⚠️ ~$20/mo Codespaces |
| **Cross-device** | ⚠️ SSH clients only | ✅ Telegram anywhere |
| **Session persistence** | ✅ tmux keeps alive | ✅ Codespaces persists |
| **Beginner friendly** | ✅ Follow steps | ❌ Requires coding |

---

## Pete Sena's Approach - Deep Dive

### Philosophy
> "The point is removing friction between an idea and a working prototype. Agentic coding makes output cheap. Context is the expensive part."

### The Stack

| Tool | Purpose | Cost |
|------|---------|------|
| **Tailscale** | VPN access to Mac from anywhere | Free tier |
| **tmux** | Keep terminal sessions alive across disconnects | Free |
| **Termius** | iOS SSH client with good UX | Free tier |
| **Claude Max** | Higher usage limits | $200/mo |

### Workflow Pipeline
```
Idea → Spec → Plan → Approval → Build → PR → Preview URL → Ship
```

Uses Claude Code slash commands and hooks to enforce structure.

### Why Mac as Hub?
1. **Filesystem** - Repos, scripts, secrets all in expected places
2. **Long-running** - Work survives network drops, phone locks
3. **Toolchain** - Git, Node, linters, formatters already installed

### Setup Steps

**On Mac:**
```bash
# 1. Enable SSH
# System Settings > General > Sharing > Remote Login ON

# 2. Install tmux
brew install tmux

# 3. Create persistent session
tmux new -s claude -n claude

# 4. Enable mouse scrolling
printf '\nset -g mouse on\n' >> ~/.tmux.conf
tmux source-file ~/.tmux.conf
```

**On iPhone:**
1. Install Tailscale, sign in, confirm Mac appears online
2. Install Termius
3. Create host: Mac's Tailscale IP, port 22, your username
4. Connect and run: `tmux attach -t claude`

### Known Limitations
- Termius struggles with very long Claude responses
- Mac must stay powered on
- No voice input capability
- Text-only interface

---

## Cloud-First Approach - Deep Dive

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLOUD (Always On)                       │
│  ┌───────────────┐    ┌───────────────┐    ┌────────────────┐  │
│  │ Telegram Bot  │───►│ Claude Code   │◄──►│ GitHub Repos   │  │
│  │ (voice+text)  │    │ (Codespaces)  │    │ (file storage) │  │
│  └───────────────┘    └───────────────┘    └────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
         ▲                      │
         │                      ▼ (when online)
    ┌─────────┐          ┌─────────────┐
    │ iPhone  │          │     Mac     │
    │         │          │  Tailscale  │
    └─────────┘          │  Local files│
                         └─────────────┘
```

### The Stack

| Tool | Purpose | Cost |
|------|---------|------|
| **Telegram Bot** | Voice & text interface | Free |
| **GitHub Codespaces** | Always-on Claude Code | ~$18-36/mo |
| **Tailscale** | Optional Mac connection | Free tier |
| **Python bridge** | Connect Telegram to Claude | Custom code |

### Why Cloud as Hub?
1. **Always available** - Works when Mac is off/asleep
2. **Voice input** - Telegram handles voice-to-text
3. **Cross-device** - Same interface on iPhone, Mac, web
4. **Version control** - Files in GitHub automatically

### Requirements to Build
- Python development skills
- Telegram Bot API knowledge
- Webhook or polling architecture
- Codespaces CLI familiarity

### Estimated Build Time
- Minimum viable version: 10-20 hours
- With Mac routing: +5-10 hours
- Polish and error handling: +5-10 hours

---

## Evaluation Against Our Requirements

| Requirement | Pete's Approach | Cloud-First |
|-------------|-----------------|-------------|
| **Priority #0: Voice input** | ❌ FAILS | ✅ Meets |
| **Works when Mac off** | ❌ FAILS | ✅ Meets |
| **Real-time response** | ✅ Meets | ⚠️ Partial |
| **Cross-device sync** | ⚠️ Partial | ✅ Meets |
| **Beginner can set up** | ✅ Yes | ❌ No |
| **Local file access** | ✅ Full | ⚠️ Limited |

---

## Recommendation

### For Immediate Use (Today)
**Start with Pete's approach:**
- Get mobile Claude Code working in 1-2 hours
- Learn tmux, Tailscale fundamentals
- Prove the workflow before adding complexity

### For Voice + Always-On (Future)
**Build hybrid architecture:**
1. Keep Pete's setup for when you're at Mac
2. Add Telegram bot pointing to Codespaces
3. Route to Mac via Tailscale when it's online

### Hybrid End State
```
┌─────────────────────────────────────────┐
│         Telegram Bot (Interface)        │
│    - Voice messages (Priority #0)       │
│    - Text messages                      │
│    - Cross-device (iPhone/Mac/Web)      │
└──────────────┬──────────────────────────┘
               │
        Python Bridge Script
               │
         ┌─────┴─────┐
         │           │
    Codespaces    Mac (via Tailscale)
    (fallback)    (preferred when online)
         │           │
    Cloud files   Local files
```

---

## Sources

- [Pete Sena's Article](https://petesena.medium.com/how-to-run-claude-code-from-your-iphone-using-tailscale-termius-and-tmux-2e16d0e5f68b)
- [Brian Sunter's Mobile Setup Tips](https://x.com/Bsunter/status/1940249686574866909)
- [Claude Code from Phone - Muhammad Azeez](https://mazeez.dev/posts/claude-code-from-phone/)
- [Sameer Halai's tmux + Tailscale Guide](https://sameerhalai.com/blog/access-your-desktop-claude-code-session-from-your-phone-using-tmux-tailscale/)
- [Tailscale](https://tailscale.com/)
- [Termius iOS](https://termius.com/)
- [GitHub Codespaces](https://github.com/features/codespaces)
