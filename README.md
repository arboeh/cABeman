🇬🇧 **English** | [🇩🇪 Deutsch](README.de.md)

# lesstokens_for_vscode
Based on caveman. Global prompt files configured for VS Code.

> English translation of [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode).



# Token-Saving Prompts for Copilot Chat

> Based on the ideas of [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman), adapted for VS Code GitHub Copilot Chat.  
> Core idea: use system-level prompts to constrain the LLM's output style, removing filler and redundant phrasing while keeping all technical substance, saving ~65% of output tokens.

---

## 1. File List

All files live in the **VS Code user-level directory**. They work across projects and do not pollute any repository.

```
%APPDATA%\Code\User\prompts\
├── caveman-lite.instructions.md   ← 🔄 Auto-injected (applies to every chat)
├── caveman-lite.prompt.md         ← 📋 On demand: 🪶 Lite
├── caveman-full.prompt.md         ← 📋 On demand: 🪨 Full
├── caveman-ultra.prompt.md        ← 📋 On demand: 🔥 Ultra
├── caveman-micro.prompt.md        ← 📋 On demand: 🤏 Micro
└── caveman-wenyan.prompt.md       ← 📋 On demand: 📜 Wenyan (Classical Chinese)
```

> Windows path: `C:\Users\{username}\AppData\Roaming\Code\User\prompts\`

| File | Type | Purpose |
|------|------|---------|
| `caveman-lite.instructions.md` | Auto-injected | `applyTo: "**"` matches all files, so it **takes effect in every Copilot chat automatically** |
| `caveman-lite.prompt.md` | On demand | 🪶 Cut fluff, keep grammar, professional and tight |
| `caveman-full.prompt.md` | On demand | 🪨 No articles, fragment-style phrasing, classic caveman style |
| `caveman-ultra.prompt.md` | On demand | 🔥 Telegraphic maximum compression, abbreviations + arrow causality chains |
| `caveman-micro.prompt.md` | On demand | 🤏 Minimal six-line ruleset, the whole caveman idea in a compact snippet |
| `caveman-wenyan.prompt.md` | On demand | 📜 Classical Chinese compression, 80-90% character reduction |

**Difference between the two file types**:
- `.instructions.md`: injected automatically; `applyTo` matches file types, no manual trigger needed
- `.prompt.md`: chosen on demand; only takes effect when you explicitly reference it in the Chat input

**Why only Lite is auto-injected**:
- A file with `applyTo: "**"` applies to every chat and every file type, so its level has to be low-risk. Lite keeps normal grammar and stays readable in technical discussions.
- Stronger levels (Full, Ultra, Wenyan) can make explanations ambiguous or confusing, and Wenyan would answer in Classical Chinese all the time.
- Several `.instructions.md` files with `applyTo: "**"` would all be injected at once. Which one wins in a conflict is not guaranteed, so switching levels per chat with `#` would stop working reliably.

**Changing the default level**:
- Replace the body of `caveman-lite.instructions.md` with another level, e.g. the `caveman-micro` rules. If you do, rename the file (e.g. `caveman-default.instructions.md`) so the name matches the content.
- To apply a stricter level only to some files, narrow the scope, e.g. `applyTo: "**/*.md"` or `applyTo: "src/**"`.
- To apply it to a single project only, put the rules in `.github/copilot-instructions.md` instead (see the tip in section 3).

---

## 2. Setup

### Step 1: Create the directory

```powershell
mkdir "$env:APPDATA\Code\User\prompts"
```

### Step 2: Create the auto-injected file (the core; effective as soon as it exists)

Create `caveman-lite.instructions.md`:

```markdown
---
applyTo: "**"
---

Reply concisely and efficiently. Drop filler words (just/really/basically/simply put), pleasantries (sure thing/happy to help), and hedging. Keep sentences short and punchy; prefer short synonyms. Keep technical terms exact and code blocks unchanged. Pattern: [thing] [action] [reason]. [next step]. Return to normal wording for security warnings and irreversible operations.
```

### Step 3 (optional): Create the on-demand level files

Put the 5 `.prompt.md` files in the same directory. Their contents are in the appendix of this document.

### Step 4 (optional): settings.json

Instructions and prompt files from the user-level prompts directory work without any extra setting in current VS Code versions. The only setting worth adding is the request limit for Agent mode (the default is 25). Put it in the project's `.vscode/settings.json` or in your user settings:

```jsonc
{
  // Max requests per session in Agent mode (default: 25)
  "chat.agent.maxRequests": 50
}
```

> The original guide also set `chat.promptFiles` and `chat.useAgentSkills`. According to the current VS Code docs, `chat.promptFiles` is not a documented setting, and `chat.useAgentSkills` (default: `true`) only switches on **Agent Skills** (`SKILL.md` folders). That is a separate feature and unrelated to `.prompt.md` files, so both settings are no longer needed here.

---

## 3. Usage

### Default behavior (automatic)

`caveman-lite.instructions.md` is injected globally via `applyTo: "**"`, so it **takes effect as soon as you open any Copilot Chat conversation**. No action needed.

### Referencing prompt files with `#` (upgrade on demand)

In the Copilot Chat input, the `#` command lets you reference `.prompt.md` files:

```
# Steps:
1. Type # in the Chat input
2. Choose "Prompt..." from the popup list (or type the file name to fuzzy-match)
3. Pick the .prompt.md file for the level you want
4. Type your question and send
```

**Example inputs**:

```
#caveman-full Explain how Vue's nextTick works
```

```
#caveman-ultra What performance problems does this code have?
```

```
#caveman-wenyan pinia vs vuex
```

> A `.prompt.md` referenced via `#` only applies to the **current conversation** and does not affect others. After you close the conversation, the default lite level is back in effect.

### Quick reference

| Scenario | Action | Effect |
|----------|--------|--------|
| **Everyday (default)** | Nothing to do | `.instructions.md` auto-injects the lite level |
| **Upgrade to Full** | Type `#` → pick `caveman-full` | Current conversation switches to caveman mode |
| **Upgrade to Ultra** | Type `#` → pick `caveman-ultra` | Telegraphic maximum compression |
| **Minimal ruleset** | Type `#` → pick `caveman-micro` | Compact six-line caveman rules |
| **Switch to Wenyan** | Type `#` → pick `caveman-wenyan` | Classical Chinese output |
| **Stop** | Say "normal mode" or "stop concise" in the chat | Back to standard replies |

> **Tip:** The `caveman-micro` snippet also works as a project-level, always-on file. Paste its body (without the front matter) into `.github/copilot-instructions.md` for Copilot, or into `CLAUDE.md` for Claude Code.

---

## 4. Comparison

Same question: "Difference between Vue ref and reactive"

| Level | Sample reply |
|-------|--------------|
| **No caveman** | "Of course! `ref` and `reactive` are both reactivity tools in Vue 3's Composition API. Simply put, `ref` is mainly used for primitive values, and it wraps the value in an object with a `.value` property…" |
| **🪶 Lite** | "`ref` wraps primitives, access via `.value`. `reactive` is for objects, access properties directly. Choose: ref for primitives, reactive for objects." |
| **🪨 Full** | "`ref` wrap primitive, need `.value`. `reactive` for object, direct access. Primitive → ref, object → reactive." |
| **🔥 Ultra** | "`ref` → primitive + `.value`. `reactive` → obj, direct access." |
| **📜 Wenyan** | "ref封基值，取以.value。reactive封物，直取其屬。" (Classical Chinese: "ref wraps base values, take via .value. reactive wraps objects, take their properties directly.") |

---

## 5. Notes

1. `applyTo: "**"` in `.instructions.md` matches all file types, so it applies globally and automatically
2. `.prompt.md` files are not injected automatically; the user must reference them in Chat with `#`
3. All files live in the user-level directory and **work across projects**, with no need to repeat the setup in each repository
4. Security warnings and irreversible operations automatically return to normal wording, so critical information is never obscured

---

## Appendix: Full contents of each level's .prompt.md

### A. caveman-lite.prompt.md

```markdown
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
```

### B. caveman-full.prompt.md

```markdown
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
```

### C. caveman-ultra.prompt.md

```markdown
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
```

### D. caveman-micro.prompt.md

```markdown
---
description: "🤏 Caveman Micro — Minimal ruleset: the whole caveman idea in six lines"
agent: ask
---

# Token efficiency
Respond like smart caveman. Cut all filler, keep technical substance.
- Drop articles (a, an, the), filler (just, really, basically, actually).
- Drop pleasantries (sure, certainly, happy to).
- No hedging. Fragments fine. Short synonyms.
- Technical terms stay exact. Code blocks unchanged.
- Pattern: [thing] [action] [reason]. [next step].
```

### E. caveman-wenyan.prompt.md

```markdown
---
description: "📜 Wenyan mode — Classical Chinese compression: the most efficient human language, 80-90% character reduction"
agent: ask
---

Reply in Classical Chinese (文言文). Keep all technical substance; compress maximally in classical style.

## Rules

Classical sentence structure, verb first, subject often omitted. Use classical particles (之/乃/為/其/則/故/以/而).
Modern technical terms, code blocks, commands, paths, and URLs stay verbatim and untranslated.
Use Classical Chinese for prose only; technical content stays exact.

## Intensity levels

### wenyan-lite (semi-classical)
Cut fluff but keep grammatical structure, in a classical register.
Example — "Why does the component re-render?"
Answer: "組件頻重繪，以每繪新生對象參照故。以 `useMemo` 包之。"
(English gloss, not part of the output: "The component repaints often, because every paint creates a new object reference. Wrap it with `useMemo`.")

### wenyan-full (maximum classical terseness, default)
Fully Classical Chinese. 80-90% character reduction.
Example — "Why does the component re-render?"
Answer: "物出新參照，致重繪。`useMemo` 包之。"
(English gloss, not part of the output: "The object yields a new reference, causing a repaint. Wrap it with `useMemo`.")

### wenyan-ultra (extreme)
Extreme abbreviation while keeping the classical Chinese feel.
Example — "Why does the component re-render?"
Answer: "新參照→重繪。`useMemo`。"
(English gloss, not part of the output: "New reference → repaint. `useMemo`.")

Switch levels with `/caveman wenyan-lite`, `/caveman wenyan`, `/caveman wenyan-ultra`.
Default is wenyan-full.

## Auto-restore

Security warnings, irreversible operations: confirm in plain modern language. Resume Classical Chinese afterwards.

## Boundaries

Code/commits written normally. "Stop wenyan" or "normal mode": revert.
```
