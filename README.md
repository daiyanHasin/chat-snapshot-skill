# 💾 chat-snapshot — Claude Skill

> Export your entire Claude conversation as a compact JSON file. Resume it in Claude, ChatGPT, Gemini, or any LLM — no re-explaining, no context lost.

**Built for free-tier users** who hit daily/weekly quota limits and need to continue work in a new chat without losing everything.

---

## The Problem

You're deep into a coding session with Claude. You've explained your stack, made decisions, fixed bugs, built things. Then — quota finished. You start a new chat and have to re-explain everything from scratch. The prompt gets longer every time. It's exhausting.

## The Solution

Type `/export` at any point. Claude compresses your **entire conversation** into a small JSON file named after your project. When you're ready to continue — in Claude, ChatGPT, or any other AI — resume in one step. No re-explaining needed.

---

## Commands

| Command | What it does |
|---|---|
| `/export` | Summarizes the full chat → outputs a JSON snapshot named after your topic |
| `/snapshot` `/save` `/compress` | Same as `/export` |

---

## How to Resume (differs by LLM)

### In Claude (with skill installed)
1. Start a new chat
2. Upload your `.json` snapshot file
3. Say **"resume from this snapshot"**
4. Claude reads it and restores full context instantly

> ⚠️ The `/resume` command only works if the skill is installed in Claude. If you're starting fresh, just upload the file and say "resume" — Claude will handle the rest.

### In ChatGPT / Gemini / any other LLM
No upload needed. No skill needed.
1. Open your `.json` file
2. Copy the **`resume_prompt`** field (it's plain English, designed for this)
3. Paste it as your **first message** in the new chat
4. The AI reads it and picks up where you left off

The `resume_prompt` is fully self-contained — it works everywhere.

---

## How to Install

### On Claude.ai (Web/Desktop/Mobile)

1. Download [`SKILL.md`](./SKILL.md)
2. Go to **claude.ai → Settings → Customize → Skills**
3. Click **Upload Skill** → select `SKILL.md`
4. Toggle it **ON**
5. Make sure **Code Execution** is enabled in Settings → Capabilities

Now type `/export` in any chat.

### On Claude Code (Terminal)

```bash
mkdir -p ~/.claude/skills/chat-snapshot
cp SKILL.md ~/.claude/skills/chat-snapshot/
cp -r references ~/.claude/skills/chat-snapshot/
```

Restart Claude Code. Type `/export` in any session.

---

## How It Works

### /export

1. Claude reads every message from start to bottom
2. Compresses it into a structured JSON — goal, progress, artifacts, bugs, next steps, key facts
3. Generates a **topic-based filename** (e.g. `python-finance-tracker-cli.json`) — never a generic name
4. Includes a `resume_prompt` field: plain English, works in any LLM without the skill

### Resuming in Claude

Upload the `.json` file in a new chat and say "resume from this snapshot." Claude reads the file and restores full context — no `/resume` command required if starting fresh.

### Resuming anywhere else

Copy the `resume_prompt` from the JSON and paste it as your first message. No file upload, no special commands.

---

## Example Snapshot

```json
{
  "snapshot_version": "1.0",
  "suggested_filename": "python-finance-tracker-cli.json",
  "goal": "Build a Python CLI personal finance tracker with multi-currency support.",
  "context": "Python 3.11, flat JSON storage, USD + BDT currencies, no frameworks.",
  "progress": [
    "Transaction dataclass designed",
    "add/filter/totals functions complete",
    "CLI menu built",
    "December month bug fixed"
  ],
  "artifacts": [
    { "name": "finance_tracker.py", "status": "complete" }
  ],
  "open_issues": [
    "Data doesn't persist between runs",
    "Currency rates hardcoded"
  ],
  "next_steps": [
    "Wire up data.json loading on startup"
  ],
  "resume_prompt": "I'm going to give you my conversation context from a previous chat. Please read it and resume from where we left off.\n\nPROJECT: Python CLI finance tracker\nSTACK: Python 3.11, flat JSON, USD + BDT\nDONE: Transaction class, CLI menu, monthly filter, December bug fixed\nARTIFACTS: finance_tracker.py (complete)\nOPEN ISSUES: no persistence between runs, hardcoded currency rates\nNEXT ACTION: wire up data.json loading on startup\n\nPlease confirm you've read this and tell me what we'll work on first."
}
```

See [`references/sample-snapshot.json`](./references/sample-snapshot.json) for a full example.

---

## Pro Tips

- **Export every 20–30 messages** — don't wait until quota is almost gone. Exporting uses tokens too.
- **Incremental exports** — mention your previous snapshot when exporting. Claude will only summarize what changed, saving tokens.
- **Filename = your project name** — the skill generates a topic-based filename automatically. Rename it if needed.
- **`resume_prompt` is universal** — paste it into any AI, no file needed. This is the most portable part of the snapshot.

---

## File Structure

```
chat-snapshot-skill/
├── SKILL.md                           ← Upload this to Claude
├── README.md                          ← This file
├── LICENSE                            ← MIT
└── references/
    ├── sample-snapshot.json           ← Full example snapshot
    └── resume-prompt-template.md      ← Guide for resume_prompt field
```

---

## Why This Exists

- 🆓 **Free-tier users** who hit weekly limits and lose context
- 🔄 **Multi-LLM users** switching between Claude, ChatGPT, Gemini
- 📁 **Long projects** where context grows too big to repeat
- 🧠 **Anyone** who wants a portable, persistent record of AI-assisted work

---

## License

MIT — free for everyone, forever.

---

## Contributing

PRs welcome. If you improve the SKILL.md prompt, add fields to the schema, or test it on a new LLM, open a PR with before/after examples.
