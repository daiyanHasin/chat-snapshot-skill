# Resume Prompt Template

The `resume_prompt` field in the snapshot is the most important field.
It is a **self-contained, paste-ready prompt** that works in ANY LLM — not just Claude.

## Template

```
Here is my context snapshot. Resume from where we left off:

PROJECT: <1-line project description>
STACK: <language, frameworks, tools>
STATUS: <what is done>
OPEN ISSUES: <bugs or blockers>
NEXT ACTION: <exactly what to do next>

Please confirm you've understood the context and ask me what I want to work on.
```

## Rules for writing a good resume_prompt

- Under 200 words — every word must earn its place
- Written in second person (tell Claude what to do, not what happened)
- Include the single most important next action explicitly
- Mention the language/stack so Claude doesn't guess
- End with a call to action ("confirm context" or "let's continue with X")

## Example (bad)

> We were working on a finance tracker. There were some bugs. The CLI is done.

## Example (good)

> Here is my context snapshot. Resume from where we left off:
> PROJECT: Python CLI personal finance tracker (no frameworks, flat JSON storage)
> STACK: Python 3.11, data.json for persistence, USD + BDT currencies
> STATUS: Transaction class done, add/filter/totals/CLI all working, December bug fixed
> OPEN ISSUES: data.json not loading on startup, currency rates hardcoded, no file error handling
> NEXT ACTION: Wire up data.json loading on startup so transactions persist between runs
> Please confirm you've understood the context and we'll continue.
