---
name: chat-snapshot
description: >
  Compresses and exports the entire current conversation into a compact, portable JSON file
  that can be downloaded and used to resume context in any new chat or LLM (Claude, ChatGPT, Gemini, etc.).
  Also restores context from a previously uploaded JSON snapshot file.

  TRIGGER this skill automatically when the user types:
  - /export — compress entire chat into a downloadable JSON snapshot
  - /snapshot, /save, /compress — same as /export
  - "save my chat", "export context", "compress this conversation"
  - "running out of tokens", "context is full", "quota is low", "save before I run out"

  ALSO TRIGGER when the user uploads a .json file and says anything like:
  - "resume", "restore context", "load my snapshot", "continue from this"
  - "here is my context", "here is my json", "I uploaded my snapshot"
  - or simply uploads a .json file without saying anything — check if it looks like a chat snapshot

  Use this skill even if the user phrases it casually like "save this" or "I want to continue later".
---

# Chat Snapshot Skill

Saves and restores full conversation context as a compact, portable JSON file.
Works across Claude, ChatGPT, Gemini, or any LLM.

---

## Command: /export

When the user types `/export` (or `/snapshot`, `/save`, `/compress`):

### Step 1 — Warn about cost
Say briefly: "Compressing now — this uses some tokens to read the full chat. Save early and often next time!"

### Step 2 — Analyze the full conversation
Read the ENTIRE conversation from the very first message to now. Do not skip anything.

### Step 3 — Generate a filename
Derive a short, meaningful filename from the conversation topic. Use the first 4–6 words of the goal, lowercase, hyphenated. Examples:
- "python-finance-tracker-cli"
- "claude-skill-chat-snapshot"
- "react-dashboard-dark-mode"

Never use "chat-snapshot" as the filename. Always reflect the actual topic.

### Step 4 — Build the JSON snapshot
Output ONLY this exact JSON structure, filled in accurately. Be ruthless about compression — every field must be concise. No filler, no repetition.

```json
{
  "snapshot_version": "1.0",
  "suggested_filename": "<topic-based-filename>.json",
  "exported_at": "<ISO timestamp if known, else 'unknown'>",
  "llm_source": "Claude",
  "goal": "<1-2 sentence summary of what the user is trying to accomplish overall>",
  "context": "<Key background: who the user is, their stack, constraints, preferences, decisions already made>",
  "progress": [
    "<Completed milestone 1>",
    "<Completed milestone 2>"
  ],
  "artifacts": [
    {
      "name": "<filename or component name>",
      "language": "<language if code>",
      "description": "<what it does>",
      "status": "complete | partial | broken"
    }
  ],
  "open_issues": [
    "<Bug or unresolved question 1>",
    "<Bug or unresolved question 2>"
  ],
  "next_steps": [
    "<Immediate next action>",
    "<Following action>"
  ],
  "key_facts": {
    "<any important technical detail>": "<value>",
    "<language/framework/tool>": "<version or note>"
  },
  "full_summary": "<3-5 sentence narrative of the entire conversation so far, enough for a new LLM to understand the full picture>",
  "resume_prompt": "<See format below>"
}
```

### Resume prompt format
The `resume_prompt` must be fully self-contained — it should work when pasted into ANY LLM even if the skill is not installed. Use this format, under 200 words:

```
I'm going to give you my conversation context from a previous chat. Please read it carefully and resume helping me from where we left off. Do not ask me to re-explain anything below.

PROJECT: <1-line description>
STACK: <language, frameworks, tools, versions>
DONE: <bullet list of completed work>
ARTIFACTS: <files/components created and their status>
OPEN ISSUES: <bugs or unresolved questions>
NEXT ACTION: <exactly what to do next, specific>

Please confirm you've read this and tell me what we'll work on first.
```

**Do NOT include any explanation inside the JSON. Output only valid JSON.**

### Step 5 — Instruct the user to save it
After outputting the JSON, say:

> ✅ **Snapshot ready.**
> Save the JSON above as **`<suggested_filename>`** (shown in the `suggested_filename` field).
>
> **To resume in Claude:** Start a new chat, upload the file, and say "resume from this snapshot."
> **To resume in ChatGPT / Gemini / any LLM:** Copy the `resume_prompt` field and paste it as your first message. No upload needed.

---

## Resuming from a snapshot (Claude only)

When the user uploads a `.json` file and it looks like a chat snapshot (has fields like `goal`, `progress`, `next_steps`), OR when they say "resume", "restore", "continue from this", "here is my snapshot":

### Step 1 — Parse the snapshot
Read the uploaded or pasted JSON.

### Step 2 — Restore context silently
Do NOT ask the user to explain anything. Treat the snapshot as full working memory immediately.

### Step 3 — Confirm restoration
Reply with:

> 🔁 **Context restored.**
> **Goal:** `<goal>`
> **Done:** `<progress as bullets>`
> **Open issues:** `<open_issues as bullets>`
> **Up next:** `<next_steps[0]>`
>
> Ready — what do you want to work on?

---

## Incremental Export

If the user has a previous snapshot and wants to update it:
- Ask: "Do you have a previous snapshot? Upload it and I'll only summarize what changed."
- If yes: read the old JSON, identify only what is new or changed since then, merge and output the full updated JSON.
- This saves tokens — no need to re-summarize unchanged content.

---

## Tips printed after every /export

Always append this after the snapshot:

> 💡 **Pro tips:**
> - Export every 20–30 messages — don't wait until quota is almost gone
> - **In ChatGPT/Gemini:** just paste the `resume_prompt` field — no upload, no skill needed
> - **In Claude:** upload the JSON file and say "resume from this snapshot"
> - For updates, keep your last snapshot handy to do an incremental export

---

## Reference files
- `references/sample-snapshot.json` — example of a complete snapshot output
- `references/resume-prompt-template.md` — guide for writing good resume prompts
