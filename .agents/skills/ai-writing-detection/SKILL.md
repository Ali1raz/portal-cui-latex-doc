---
name: ai-writing-detection
description: Detects AI-generated text and rewrites/de-AIs documents to sound natural and human. Use this skill whenever the user wants to: check if writing sounds AI-generated, scan text for AI tells, clean up AI writing, humanize a document, remove AI patterns, rewrite a doc to sound less like ChatGPT, or de-AI any text. Triggers on phrases like "does this sound AI?", "humanize this", "de-AI this", "rewrite this to sound human", "check for AI patterns", or whenever a file or text is provided alongside intent to detect or fix AI-like writing.
user-invocable: true
argument-hint: "[text or FILE <filename>] [--detect | --rewrite | --both]"
---

# AI Writing Detection & Rewriting

Two modes, one skill:

- **Detect** — Scan text and report AI patterns found
- **Rewrite** — Rewrite the text to remove AI patterns, preserving meaning and voice
- **Both** (default when no flag) — Detect first, then rewrite

If the user says "de-AI this", "humanize this", "rewrite this", or provides a doc/file and asks to fix it → use **Rewrite** mode.  
If the user says "check this", "does this sound AI?", "scan for AI tells" → use **Detect** mode.  
If unclear → run **Both**.

---

## Step 1: Read the AI Pattern Reference

Before running either mode, read the full pattern list from:

→ `references/ai-patterns.md`

This contains the canonical list of words, phrases, and structural patterns that signal AI writing. Load it into context before analyzing or rewriting anything.

---

## Step 2: Detect Mode

Scan the text against the patterns in `references/ai-patterns.md`.

### Output format

```
## AI Writing Detection Report

**Overall assessment:** [Clean / Minor AI signals / Moderate AI signals / Strong AI signals]

### Em Dashes
[Count found] — [list each instance]

### Flagged Words & Phrases
| Found | Category | Suggested replacement |
|-------|----------|-----------------------|
| ...   | ...      | ...                   |

### Structural Patterns
- [Any structural patterns found, e.g. "Whether you're a X, Y, or Z..."]

### Summary
[2–3 sentence plain English summary of what was found and how noticeable it is]
```

**Scoring guide:**

- 0 flags → Clean
- 1–3 flags → Minor AI signals
- 4–8 flags → Moderate AI signals
- 9+ flags → Strong AI signals

---

## Step 3: Rewrite Mode

Rewrite the text to eliminate AI patterns while:

1. **Preserving meaning** — Every idea in the original stays in the rewrite
2. **Preserving structure** — Don't restructure paragraphs unless absolutely needed
3. **Preserving voice** — Match the formality and tone of the original
4. **Not over-correcting** — Don't replace every word; only fix flagged patterns
5. **Not introducing new AI patterns** — Check your own output before finishing

### Rewriting rules

- Replace flagged words with simpler, more direct alternatives (see `references/ai-patterns.md`)
- Convert em dashes to commas, parentheses, or colons as appropriate
- Break up repetitive sentence structures if found (vary length naturally)
- Remove filler intensifiers (absolutely, truly, certainly, etc.) unless removing them breaks grammar
- Do NOT add new content, examples, or elaboration — rewrite only what's there
- Do NOT change proper nouns, technical terms, or quoted text

### Output format

Return the full rewritten text, then a brief change log:

```
[Rewritten text here — complete, ready to use]

---
**Changes made:**
- Replaced "leverage" → "use" (×2)
- Converted 3 em dashes to commas
- Removed "robust", "comprehensive" → "thorough", "complete"
- Removed filler: "truly", "essentially"
```

---

## Step 4: File Handling

If the user provides a file:

- **.txt / .md** — Read with bash `cat`, detect/rewrite, output as same format
- **.docx** — Read the skill at `/mnt/skills/public/docx/SKILL.md` before proceeding. Extract text, rewrite, then write back as .docx and present the file.
- **.pdf** — Read the skill at `/mnt/skills/public/pdf-reading/SKILL.md`. Extract text, rewrite, present as .txt or .md (PDF regeneration is complex — ask user their preferred output format)
- **pasted text** — Process inline, show output in conversation

---

## Quick Reference: Top AI Signals

The most common and detectable patterns (full list in `references/ai-patterns.md`):

**#1 tell:** Em dashes (—) used more than once per page  
**Top verbs:** delve, leverage, utilise, facilitate, foster, underscore  
**Top adjectives:** robust, comprehensive, pivotal, seamless, nuanced, transformative  
**Top openers:** "In today's...", "It's important to note...", "In the realm of..."  
**Top closers:** "In conclusion...", "At the end of the day...", "By doing X, you can Y..."  
**Filler words:** absolutely, essentially, truly, significantly, undoubtedly
