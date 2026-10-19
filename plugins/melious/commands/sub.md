---
description: Run a sub-task on Melious instead of Claude (read-only agent, Flash by default)
argument-hint: "[-m glm] [-w] <task>"
allowed-tools: Bash
---
Run this as a Melious sub-agent and report the result, verifying any claim against the code before repeating it:

!`"${CLAUDE_PLUGIN_ROOT}/bin/mel-sub" $ARGUMENTS`
