🇬🇧 **English** | [🇩🇪 Deutsch](README.de.md)

# cABeman
Based on caveman. Token-saving prompt files for **Claude Code** and **GitHub Copilot Chat** in VS Code.

> English translation of [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode), extended with a Claude Code variant.

# Token-Saving Prompts for Claude Code and Copilot Chat

> Based on the ideas of [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman).  
> The idea for this package comes from [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode) (the Copilot Chat prompt files, translated here) and [kuba-guzik/caveman-micro](https://github.com/kuba-guzik/caveman-micro) (the Micro ruleset used as the default). See [Credits](#credits).  
> Core idea: use prompts to constrain the LLM's output style, removing filler and redundant phrasing while keeping all technical substance, saving ~65% of output tokens (figure from the original guide, not measured here).

The package comes in two variants. Both have the same idea: a small **always-on** rule set (**Micro**) plus **on-demand levels** you switch to when you want it terser:

| Level | Style |
|-------|-------|
| 🤏 **Micro** (default, always on) | Six-line ruleset: no articles/filler/pleasantries, fragments fine, technical terms exact |
| 🪶 **Lite** | Cut fluff, keep grammar, professional and tight |
| 🪨 **Full** | No articles, fragment-style phrasing, classic caveman style |
| 🔥 **Ultra** | Telegraphic maximum compression, abbreviations + arrow causality chains |

`less_tokens.zip` contains both variants:

```
less_tokens.zip
├── claude-code/                    ← Claude Code
│   ├── rules/caveman.md            ← 🔄 always on (Micro)
│   └── skills/
│       ├── caveman-lite/SKILL.md   ← 📋 on demand: /caveman-lite
│       ├── caveman-full/SKILL.md
│       └── caveman-ultra/SKILL.md
└── copilot/                        ← GitHub Copilot Chat
    ├── prompts/
    │   ├── caveman-micro.instructions.md   ← 🔄 always on (Micro)
    │   ├── caveman-lite.prompt.md          ← 📋 on demand: /caveman-lite
    │   ├── caveman-full.prompt.md
    │   └── caveman-ultra.prompt.md
    └── .vscode/settings.json               ← optional
```

For Claude Code, the same skills and rule also sit as plain files in this repository (`skills/` and `rules/`), so skill managers such as Skill-Dock can import them directly (see Option B).

---

# Part 1: Claude Code

## 1.1 Where the files go

Claude Code reads user-level files from the `.claude` folder in your home directory. The same path logic applies on every OS (`~` is your home/user profile folder):

| File | Target |
|------|--------|
| `claude-code/rules/caveman.md` | `~/.claude/rules/caveman.md` (Windows: `%USERPROFILE%\.claude\rules\caveman.md`) |
| `claude-code/skills/caveman-<level>/` | `~/.claude/skills/caveman-<level>/SKILL.md` |

Both locations are documented by Claude Code:
- **Rules:** all `.md` files in `~/.claude/rules/` apply to every project. Rules without a `paths` front matter are loaded at launch.
- **Skills:** a personal skill `~/.claude/skills/<name>/SKILL.md` becomes the slash command `/<name>`. With `disable-model-invocation: true` (set in these files) it only runs when you type it; Claude does not load it on its own.

## 1.2 Setup

Two options. Option A installs everything (rule + skills). Option B installs only the skills, with a VS Code extension; the always-on rule still has to be copied by hand.

### Option A: Manual copy from the ZIP (everything)

Unzip `less_tokens.zip` somewhere first, then copy the files from its `claude-code` folder. If the target folders already exist, the commands add to them; same-named files are overwritten.

**Windows (PowerShell)**, run inside the unzipped folder:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\rules","$HOME\.claude\skills" | Out-Null
Copy-Item .\claude-code\rules\caveman.md "$HOME\.claude\rules\" -Force
Copy-Item .\claude-code\skills\* "$HOME\.claude\skills\" -Recurse -Force
```

**macOS / Linux (shell)**, run inside the unzipped folder:

```bash
mkdir -p ~/.claude/rules ~/.claude/skills
cp claude-code/rules/caveman.md ~/.claude/rules/
cp -r claude-code/skills/* ~/.claude/skills/
```

Or do it by hand in your file manager. Then **start a new Claude Code session**: rules are read at launch.

### Option B: Import via URL with a skill manager (skills only)

The skills are also stored as plain files in this repository (`skills/caveman-<level>/SKILL.md`, one folder per skill), so any tool that imports skills from a GitHub URL can read them. No particular tool is required.

1. Use a skill manager or extension of your choice that imports skills from GitHub repositories into the skill folder of your tool (for Claude Code: `~/.claude/skills/` or a project's `.claude/skills/`).
2. Add this repository as a source: `https://github.com/arboeh/cABeman`. If the tool wants a link to a single skill, use the folder, e.g. `https://github.com/arboeh/cABeman/tree/main/skills/caveman-full`.
3. Import the `caveman-*` skills.
4. Copy the always-on rule by hand: [`rules/caveman.md`](rules/caveman.md) to `~/.claude/rules/caveman.md` (Windows: `%USERPROFILE%\.claude\rules\caveman.md`). Skill managers install skills only.

One example is the VS Code extension [Skill-Dock](https://github.com/yen0304/Skill-Dock): per its README, it can add a GitHub repository as a source (**Skill Dock: Add Marketplace Source** or the `skilldock.marketplaceSources` setting) and import skills for Claude Code. This flow has not been tested for this guide.

> The folder-link format and whether a tool keeps the `disable-model-invocation` setting of these skills depend on the tool. After importing, check the `SKILL.md` files; if the setting is missing, Claude may also load a level on its own. Option A avoids that question.

### Always-on rule (Micro)

`~/.claude/rules/caveman.md`:

```markdown
# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
```

If you prefer a single file, paste this text into `~/.claude/CLAUDE.md`, or into a project's `CLAUDE.md` for one project only.

## 1.3 Usage

- **Default:** nothing to do. The Micro rule applies in every session.
- **Switch level:** type the skill as a slash command, then your question:

```
/caveman-full Explain how Vue's nextTick works
/caveman-ultra What performance problems does this code have?
```

| Scenario | Action | Effect |
|----------|--------|--------|
| **Everyday (default)** | Nothing to do | `rules/caveman.md` applies Micro |
| **Lite** | `/caveman-lite` | Grammar kept, fluff cut |
| **Full** | `/caveman-full` | Caveman mode |
| **Ultra** | `/caveman-ultra` | Telegraphic maximum compression |
| **Stop** | Say "normal mode" or "stop concise" | Back to standard replies |

How long a level stays active within a session is not specified by the docs; it is up to the model. Say the level or "normal mode" again if it drifts.

## 1.4 Changing the default or its scope

- Use another level as the always-on rule by replacing the body of `~/.claude/rules/caveman.md`. Rename the file if the name no longer fits.
- Limit a rule to some files with a `paths` front matter (Claude Code loads it when it works on matching files):

```markdown
---
paths:
  - "src/**"
---
```

---

# Part 2: GitHub Copilot Chat

## 2.1 File list

All files live in the **VS Code user-level directory**. They work across projects and do not pollute any repository.

```
<VS Code user data>/Code/User/prompts/
├── caveman-micro.instructions.md  ← 🔄 Auto-injected (applies to every chat)
├── caveman-lite.prompt.md         ← 📋 On demand: 🪶 Lite
├── caveman-full.prompt.md         ← 📋 On demand: 🪨 Full
└── caveman-ultra.prompt.md        ← 📋 On demand: 🔥 Ultra
```

> The `prompts` folder lives in VS Code's user data directory (`Code/User`):
>
> | OS | Path |
> |----|------|
> | Windows | `%APPDATA%\Code\User\prompts\` (i.e. `C:\Users\{username}\AppData\Roaming\Code\User\prompts\`) |
> | macOS | `$HOME/Library/Application Support/Code/User/prompts/` |
> | Linux | `$HOME/.config/Code/User/prompts/` |
>
> The VS Code docs confirm the `Code/User` directory for each OS (it is where the user `settings.json` lives). That the `prompts` subfolder sits inside it is taken from the original guide; the docs only say user-level prompt files are stored in the profile's user data. Other VS Code editions (e.g. Insiders, which uses `Code - Insiders`) can use a different folder.
>
> User-level prompt files belong to your **VS Code profile**. If you work with several profiles, each profile has its own set, so the files must be added to the profile you want to use.

| File | Type | Purpose |
|------|------|---------|
| `caveman-micro.instructions.md` | Auto-injected | `applyTo: "**"` matches all files, so it **takes effect in every Copilot chat automatically** |
| `caveman-lite.prompt.md` | On demand | 🪶 Cut fluff, keep grammar, professional and tight |
| `caveman-full.prompt.md` | On demand | 🪨 No articles, fragment-style phrasing, classic caveman style |
| `caveman-ultra.prompt.md` | On demand | 🔥 Telegraphic maximum compression, abbreviations + arrow causality chains |

**Difference between the two file types**:
- `.instructions.md`: injected automatically; `applyTo` matches file types, no manual trigger needed
- `.prompt.md`: chosen on demand; only takes effect when you explicitly run it in the Chat input

**Why only one level is auto-injected**:
- A file with `applyTo: "**"` applies to every chat and every file type, so its level has to be low-risk. Micro is the mildest set that still saves tokens.
- Stronger levels (Full, Ultra) can make explanations ambiguous or confusing.
- Several `.instructions.md` files with `applyTo: "**"` would all be injected at once. Which one wins in a conflict is not guaranteed, so switching levels per chat with a slash command would stop working reliably.

**Changing the default level**:
- Replace the body of `caveman-micro.instructions.md` with another level. If you do, rename the file so the name matches the content.
- To apply a stricter level only to some files, narrow the scope, e.g. `applyTo: "**/*.md"` or `applyTo: "src/**"`.
- To apply it to a single project only, put the rules in `.github/copilot-instructions.md` instead.

## 2.2 Setup

### Step 1: Create the directory

Create the `prompts` folder from the table above, if it does not exist yet. If it already exists, skip this step; existing files in it stay untouched. Any of these works:

- **File manager:** create the folder by hand (Explorer, Finder, or your Linux file manager).
- **VS Code:** run **Chat: New Prompt File** from the Command Palette and choose the user (profile) location. VS Code creates the file in the right place for the active profile, so you can see where it lands.
- **Windows (PowerShell):** `New-Item -ItemType Directory -Force "$env:APPDATA\Code\User\prompts"`
- **macOS / Linux (shell):** `mkdir -p` with the path from the table, e.g. `mkdir -p "$HOME/.config/Code/User/prompts"` on Linux

The `-Force` and `-p` flags make these commands succeed without an error when the folder already exists.

### Step 2: Copy the auto-injected file (the core; effective as soon as it exists)

This step is manual: unzip `less_tokens.zip` and copy `copilot/prompts/caveman-micro.instructions.md` into the `prompts` folder, or create it yourself with this content:

```markdown
---
applyTo: "**"
---

# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
```

### Step 3 (optional): Copy the on-demand level files

Copy the 4 `.prompt.md` files from `copilot/prompts/` into the same folder. Their contents are in the appendix.

### Step 4 (optional): settings.json

Instructions and prompt files from the user-level prompts directory work without any extra setting in current VS Code versions. The only setting worth adding is the request limit for Agent mode (the default is 25). Put it in the project's `.vscode/settings.json` or in your user settings:

```jsonc
{
  // Max requests per session in Agent mode (default: 25)
  "chat.agent.maxRequests": 50
}
```

> The original guide also set `chat.promptFiles` and `chat.useAgentSkills`. According to the current VS Code docs, `chat.promptFiles` is not a documented setting, and `chat.useAgentSkills` (default: `true`) only switches on **Agent Skills** (`SKILL.md` folders). That is a separate feature and unrelated to `.prompt.md` files, so both settings are no longer needed here.

## 2.3 Usage

### Default behavior (automatic)

`caveman-micro.instructions.md` is injected globally via `applyTo: "**"`, so it **takes effect as soon as you open any Copilot Chat conversation**. No action needed.

### Running prompt files with `/` (upgrade on demand)

In the Copilot Chat input, type `/` followed by the prompt name to run a `.prompt.md` file as a slash command:

```
Steps:
1. Type / in the Chat input
2. Pick the prompt from the list (or keep typing the name, e.g. /caveman-full)
3. Add your question after the command and send
```

Alternatives: run **Chat: Run Prompt** from the Command Palette, or open the `.prompt.md` file and press the play button in the editor title area.

> `#` is **not** the way to run a prompt file. In VS Code, `#` is for adding context such as `#file:` or `#tool:`.

**Example inputs**:

```
/caveman-full Explain how Vue's nextTick works
/caveman-ultra What performance problems does this code have?
```

> A `.prompt.md` run via `/` is meant for the chat you run it in. In a new chat, the default Micro level applies again unless you run a prompt there too. The docs do not say whether user-level prompt files always show up in the `/` list; if one is missing, use **Chat: Run Prompt**.

| Scenario | Action | Effect |
|----------|--------|--------|
| **Everyday (default)** | Nothing to do | `.instructions.md` auto-injects Micro |
| **Lite** | Type `/caveman-lite` | Grammar kept, fluff cut |
| **Full** | Type `/caveman-full` | Current chat switches to caveman mode |
| **Ultra** | Type `/caveman-ultra` | Telegraphic maximum compression |
| **Stop** | Say "normal mode" or "stop concise" in the chat | Back to standard replies |

---

# Comparison

Same question: "Difference between Vue ref and reactive"

| Level | Sample reply |
|-------|--------------|
| **No caveman** | "Of course! `ref` and `reactive` are both reactivity tools in Vue 3's Composition API. Simply put, `ref` is mainly used for primitive values, and it wraps the value in an object with a `.value` property…" |
| **🪶 Lite** | "`ref` wraps primitives, access via `.value`. `reactive` is for objects, access properties directly. Choose: ref for primitives, reactive for objects." |
| **🪨 Full** | "`ref` wrap primitive, need `.value`. `reactive` for object, direct access. Primitive → ref, object → reactive." |
| **🔥 Ultra** | "`ref` → primitive + `.value`. `reactive` → obj, direct access." |

These sample replies come from the original guide; they were not regenerated.

---

# Notes

1. Always-on files (`rules/caveman.md`, `*.instructions.md`) apply automatically. Level files (skills, `.prompt.md`) only run when you type their slash command.
2. All files live in user-level folders and **work across projects**, with no need to repeat the setup in each repository.
3. Security warnings and irreversible operations should return to normal wording, so critical information is never obscured.
4. The rules are written for English replies. If you ask in another language, the style still tends to carry over, but the filler-word lists will match less precisely.

---

# Credits

- [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode): the original idea and the Copilot Chat prompt files (Lite, Full, Ultra, Wenyan, in Chinese). This repository is an English and German translation, extended with a Claude Code variant. The original repository has no license file.
- [kuba-guzik/caveman-micro](https://github.com/kuba-guzik/caveman-micro) (MIT): the Micro ruleset used here as the always-on default.
- [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman): the caveman style these prompts are based on.

---

# Appendix: Full file contents

All files are in English and identical in the ZIP and here.

## Claude Code

### claude-code/rules/caveman.md

````markdown
# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
````

### claude-code/skills/caveman-lite/SKILL.md

````markdown
---
name: caveman-lite
description: "🪨 Caveman Lite — Concise mode: cut filler, keep grammar, professional but tight"
disable-model-invocation: true
---

Reply concisely and efficiently. Keep all technical substance; remove only the fluff.

## Rules

Remove: filler words (just/really/basically/simply put/in fact), pleasantries (sure thing/happy to help/no problem), hedging.
Keep full grammar and articles. Keep sentences short and punchy. Prefer short synonyms (big, not extensive; fix, not implement a solution).
Technical terms stay exact. Code blocks stay unchanged. Quote error messages exactly.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Auth middleware has a bug. Token expiry check uses `<` instead of `<=`. Fix:"

## Auto-restore clarity

Pause concise mode and use normal wording for: security warnings, confirmation of irreversible operations, multi-step sequences where fragmented phrasing could be misread, and when the user seems confused. Resume concise mode once the clear part is done.

## Boundaries

Code/commits/PRs: write normally. Revert when the user says "stop concise" or "normal mode". The level persists until switched or the session ends.
````

### claude-code/skills/caveman-full/SKILL.md

````markdown
---
name: caveman-full
description: "🪨 Caveman Full — Caveman mode: no articles, fragments, classic caveman style"
disable-model-invocation: true
---

Reply lean, like a smart caveman. All technical substance stay. Only fluff die.

## Rules

Drop: articles (a/an/the), filler (just/really/basically/simply put), pleasantries (sure/no problem/happy to help), hedging.
Fragments OK. Short synonyms (big not extensive, fix not "implement a solution"). Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Bug in auth middleware. Token expiry check use `<` not `<=`. Fix:"

## Intensity examples

Q: "Why does my React component re-render?"
A: "New object ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`."

Q: "Explain database connection pooling."
A: "Pool reuse open DB connections. No new connection per request. Skip handshake overhead."

## Auto-restore

Security warnings, irreversible-operation confirmations, user confusion: drop caveman, speak normally. Resume after clear part done.

## Boundaries

Code/commits/PRs: write normally. "Stop caveman" or "normal mode": revert. Level persists until switched or session ends.
````

### claude-code/skills/caveman-ultra/SKILL.md

````markdown
---
name: caveman-ultra
description: "🔥 Caveman Ultra — Maximum compression: telegraphic, abbreviate everything, arrow causality"
disable-model-invocation: true
---

Compress replies to the limit. Keep all technical substance. Cut words ruthlessly.

## Rules

Abbreviate everything (DB/auth/config/req/res/fn/impl/env/dep/pkg/init/param/cb).
Cut conjunctions. Use arrows for causality (X → Y). One word when one word enough.
Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[abbrev] → [result]. [action].`

Not: "The component re-renders because a new object reference is created on every render"
Yes: "Inline obj prop → new ref → re-render. `useMemo`."

## Examples

Q: "Explain database connection pooling"
A: "Pool = reuse DB conn. Skip handshake → fast under load."

Q: "Fix auth middleware token expiry"
A: "L42: token check `<` → `<=`. Off by 1s at expiry."

## Auto-restore

Security warnings/irreversible operations: speak normally. Resume ultra afterwards.

## Boundaries

Code written normally. "Stop": revert.
````

## GitHub Copilot Chat

### copilot/prompts/caveman-micro.instructions.md

````markdown
---
applyTo: "**"
---

# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
- Security warnings and irreversible actions: write normally, full sentences.
````

### copilot/prompts/caveman-lite.prompt.md

````markdown
---
description: "🪨 Caveman Lite — Concise mode: cut filler, keep grammar, professional but tight"
agent: ask
---

Reply concisely and efficiently. Keep all technical substance; remove only the fluff.

## Rules

Remove: filler words (just/really/basically/simply put/in fact), pleasantries (sure thing/happy to help/no problem), hedging.
Keep full grammar and articles. Keep sentences short and punchy. Prefer short synonyms (big, not extensive; fix, not implement a solution).
Technical terms stay exact. Code blocks stay unchanged. Quote error messages exactly.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Auth middleware has a bug. Token expiry check uses `<` instead of `<=`. Fix:"

## Auto-restore clarity

Pause concise mode and use normal wording for: security warnings, confirmation of irreversible operations, multi-step sequences where fragmented phrasing could be misread, and when the user seems confused. Resume concise mode once the clear part is done.

## Boundaries

Code/commits/PRs: write normally. Revert when the user says "stop concise" or "normal mode". The level persists until switched or the session ends.
````

### copilot/prompts/caveman-full.prompt.md

````markdown
---
description: "🪨 Caveman Full — Caveman mode: no articles, fragments, classic caveman style"
agent: ask
---

Reply lean, like a smart caveman. All technical substance stay. Only fluff die.

## Rules

Drop: articles (a/an/the), filler (just/really/basically/simply put), pleasantries (sure/no problem/happy to help), hedging.
Fragments OK. Short synonyms (big not extensive, fix not "implement a solution"). Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[thing] [action] [reason]. [next step].`

Not: "Sure, I'd be happy to help you with that. The issue you're experiencing is most likely caused by..."
Yes: "Bug in auth middleware. Token expiry check use `<` not `<=`. Fix:"

## Intensity examples

Q: "Why does my React component re-render?"
A: "New object ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`."

Q: "Explain database connection pooling."
A: "Pool reuse open DB connections. No new connection per request. Skip handshake overhead."

## Auto-restore

Security warnings, irreversible-operation confirmations, user confusion: drop caveman, speak normally. Resume after clear part done.

## Boundaries

Code/commits/PRs: write normally. "Stop caveman" or "normal mode": revert. Level persists until switched or session ends.
````

### copilot/prompts/caveman-ultra.prompt.md

````markdown
---
description: "🔥 Caveman Ultra — Maximum compression: telegraphic, abbreviate everything, arrow causality"
agent: ask
---

Compress replies to the limit. Keep all technical substance. Cut words ruthlessly.

## Rules

Abbreviate everything (DB/auth/config/req/res/fn/impl/env/dep/pkg/init/param/cb).
Cut conjunctions. Use arrows for causality (X → Y). One word when one word enough.
Technical terms exact. Code blocks unchanged. Errors quoted exact.

Pattern: `[abbrev] → [result]. [action].`

Not: "The component re-renders because a new object reference is created on every render"
Yes: "Inline obj prop → new ref → re-render. `useMemo`."

## Examples

Q: "Explain database connection pooling"
A: "Pool = reuse DB conn. Skip handshake → fast under load."

Q: "Fix auth middleware token expiry"
A: "L42: token check `<` → `<=`. Off by 1s at expiry."

## Auto-restore

Security warnings/irreversible operations: speak normally. Resume ultra afterwards.

## Boundaries

Code written normally. "Stop": revert.
````
