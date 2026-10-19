---
name: melious
description: Delegate sub-tasks to Melious (EU-hosted glm-5.3 / glm-5.3-flash) instead of spending Claude limits — fan-out reading, repo searches, second opinions, adversarial review passes, drafts and summaries. Use whenever you would spawn a sub-agent for reading or research, when the [melious] load line says gelb/rot, or when the user says "melious", "mel-sub" or "spar Claude".
---

# Melious as the sub-agent layer

Claude is the lead: it plans, decides, writes code and commits. Melious does the volume work underneath.
The `[melious]` line at session start (and again when the load level changes) tells you which mode applies.
Re-check any time with `mel-last` (in the plugin's `bin/`; the line prints its full path).

## Which tool

| Task | Command |
|---|---|
| Fan-out reading, "find where X happens", read 20 files and report | `mel-sub "…"` (agent, read-only, Flash) |
| Second opinion / adversarial review pass (Pass C) on a diff | `mel-sub -m glm "Try to BREAK git diff origin/develop...HEAD: …"` |
| Plain text work: summarise, translate, draft a mail from notes | `mel-sub -p "…" -f notes.md` (no agent, cheapest) |
| Work that must edit files | `mel-sub -w "…"` — only for clearly scoped, reviewable changes; review the diff yourself |

Models: **Flash (`glm-5.3-flash`) is the default**. Use `-m glm` (glm-5.3) only for high-value judgement: review verdicts,
plans, architecture questions. Never run sub-tasks on the lead model.

## Load levels

- **grün** — Claude for the main thread; reading fan-out, second opinions and review cross-checks go to Melious.
- **gelb** (5 h window ≥ 60 % or ahead of weekly pace) — every sub-task through `mel-sub`, including ones you would
  otherwise start as an Agent. Claude only for the main thread and writing.
- **rot** (≥ 85 %) — research and drafts through `mel-sub` too; suggest `cswap switch` if another account has room.

## Rules

- Treat Melious output as **candidates**: verify every claim against the code before you repeat or post it.
- Pass the task, the paths and the expected output format; Melious does not see this conversation.
- Long or parallel runs: `-o result.md`, run in the background, read only the result.
- Never pass the key on a command line and never print it. Key setup is once per machine: `melious auth login`.
- Personal or customer data goes to Melious only pseudonymised and only with the user's ok.
