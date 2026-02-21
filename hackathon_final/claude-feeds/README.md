# Claude Feeds - Hackathon Quick Reference
## How to Use These Documents During the Hackathon

---

## Folder Structure

```
claude-feeds/
├── README.md                    ← You are here
├── shared/                      ← Documents BOTH accounts need
│   ├── 01_PROJECT_CONTEXT.md   ← Feed this FIRST to any Claude
│   └── 02_DESIGN_SYSTEM.md     ← Design tokens, component patterns
│
├── account-1-frontend/          ← For Developer 1 (Siddharth)
│   ├── FEED_PHASE_1.md         ← Feed at Hour 0
│   ├── FEED_PHASE_2.md         ← Feed at Hour 3
│   ├── FEED_PHASE_3.md         ← Feed at Hour 6
│   └── FEED_PHASE_4.md         ← Feed at Hour 9
│
└── account-2-backend/           ← For Developers 2+3 (Saksham x2)
    ├── FEED_PHASE_1.md         ← Feed at Hour 0
    ├── FEED_PHASE_2.md         ← Feed at Hour 3
    ├── FEED_PHASE_3.md         ← Feed at Hour 6
    └── FEED_PHASE_4.md         ← Feed at Hour 9
```

---

## Quick Start Instructions

### Before Starting Each Phase:

1. **Start a NEW Claude conversation** (fresh context)
2. **Copy-paste the SHARED context first** (`shared/01_PROJECT_CONTEXT.md`)
3. **Then paste the phase-specific feed** (`FEED_PHASE_X.md`)
4. **Start coding!**

### Example Prompt Format:

```
I'm working on a hackathon project. Here's the context:

[Paste contents of 01_PROJECT_CONTEXT.md]

Now here's my current phase task:

[Paste contents of FEED_PHASE_X.md]

Let's start building. Create the files listed in the deliverables.
```

---

## Git Workflow During Phases

```bash
# At start of each phase
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-X-name  # or be-phase-X-name

# Commit every 30 minutes
git add .
git commit -m "feat(feX): description"

# At end of phase
git push -u origin feature/fe-phase-X-name
gh pr create --base develop --title "Phase X: Title"
```

---

## Phase Timing

| Phase | Start | End | Frontend | Backend |
|-------|-------|-----|----------|---------|
| 1 | Hour 0 | Hour 3 | Foundation + Theme | Foundation + DB |
| 2 | Hour 3 | Hour 6 | Classify + Chat | Classification MS |
| 3 | Hour 6 | Hour 9 | Cost + Compliance | Route Optimizer MS |
| 4 | Hour 9 | Hour 12 | Polish + Export | Integration APIs |

---

## Emergency Commands

```bash
# If something breaks, reset to develop
git checkout develop
git reset --hard origin/develop

# If you need to see what changed
git diff HEAD~1

# If merge conflict
git checkout --theirs .  # or --ours
git add .
git commit -m "fix: resolve conflicts"
```

---

## Contact During Hackathon

- All team members should be in the same room or on a call
- Communicate before merging to avoid conflicts
- If stuck on a phase for >30 mins, ask for help

**Good luck! You've got this!**
