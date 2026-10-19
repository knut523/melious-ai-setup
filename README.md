# Melious plugin for Claude Code

Lets Claude Code hand its sub-tasks to **Melious** (EU-hosted models: `glm-5.3-flash`, `glm-5.3`) instead of
spending your Claude limits, and steers the load between the two. Built for the MAXENERGY / OLAF team.

- **`mel-sub "task"`** runs a sub-agent on Melious through **[jcode](https://github.com/1jehuang/jcode)**, which is
  light on memory and tokens. It is read-only by default (`-w` allows edits) and uses Flash by default (`-m glm` for
  judgement calls). `-p` sends a plain prompt without an agent (cheapest). `-c` uses Claude Code against Melious, but
  only as a fallback if jcode is missing.
- **Load line at session start:** reads your Claude usage via [cswap](https://pypi.org/project/claude-swap/) (5-hour
  and weekly windows) and tells Claude what goes to Melious:

  | Level | When | What goes to Melious |
  |---|---|---|
  | grün | 5 h window < 60 % | reading fan-out, second opinions, review cross-checks |
  | gelb | ≥ 60 % or ahead of weekly pace | every sub-task |
  | rot | ≥ 85 % | research and drafts too, plus a hint to `cswap switch` |

  It is re-checked at most every 15 minutes and repeated only when the level changes.
- **Skill `melious`:** when and how Claude delegates, and the rule that Melious output is a candidate that has to be
  checked against the code.
- **Commands:** `/melious:auslastung` shows the load and the recommendation, `/melious:sub <task>` hands off a task.

## Install (once per machine, ~5 minutes)

```bash
# 1. Melious CLI + your own key (hidden prompt, stored in ~/.melious/credentials.json, mode 600)
curl -fsSL https://cdn.melious.ai/install.sh | bash        # https://melious.ai/docs/cli
melious auth login

# 2. jcode — the light agent mel-sub runs on (https://github.com/1jehuang/jcode, MIT)
curl -fsSL https://jcode.sh/install | bash                  # https://jcode.sh/docs

# 3. Optional: cswap for the Claude load line (and account switching)
uv tool install claude-swap

# 4. The plugin (replace <org> with the GitHub org this repo lives in)
claude plugin marketplace add <org>/melious-claude-plugin
claude plugin install melious@maxenergy-melious

# 5. Check and wire jcode to Melious (adds a [providers.melious] profile to ~/.jcode/config.toml if missing)
~/.claude/plugins/marketplaces/maxenergy-melious/plugins/melious/bin/mel-setup
```

`mel-setup` prints ✓/✗ for the Melious CLI, the key, jcode and its Melious profile, and cswap. It never writes a key.

## Use

```bash
mel-sub "Where is the meter-point limit enforced? List file:line."      # read-only agent, Flash
mel-sub -m glm "Try to BREAK the diff in git diff origin/develop...HEAD"   # judgement, glm-5.3
mel-sub -p "Summarise these notes in five bullets" -f notes.md             # plain prompt, no agent
mel-sub -w "Rename X to Y in src/a.ts and fix the imports"                 # may edit; review the diff yourself
mel-last                                                                   # load line by hand
```

Inside Claude Code you usually don't call these yourself: the skill makes Claude use `mel-sub` for fan-out and second
opinions. You can still say "do it with Melious" or run `/melious:sub …`. (`mel-sub` / `mel-last` live in the plugin's
`bin/`; the load line prints the full path.)

Thresholds: `MELIOUS_GELB` (default 60) and `MELIOUS_ROT` (default 85), percent of the 5-hour window.
`MEL_SUB_TIMEOUT` (seconds, default 1500).

## Rules

- **Your own key per person.** Ask Knut; it arrives as a one-time Vaultwarden Send link. Never paste it into a chat,
  a command line or a file — `melious auth login` reads it from a hidden prompt.
- Melious output is a **candidate**, not a verdict: verify claims against the code before repeating or posting them.
- Personal or customer data goes to Melious only pseudonymised and only with an explicit ok.

## Layout

```
.claude-plugin/marketplace.json        marketplace "maxenergy-melious"
plugins/melious/
  .claude-plugin/plugin.json
  bin/mel-sub  bin/mel-last  bin/mel-setup
  hooks/hooks.json                     SessionStart + UserPromptSubmit → mel-last --hook
  skills/melious/SKILL.md
  commands/auslastung.md  commands/sub.md
```

## License

MIT — see `LICENSE`.
