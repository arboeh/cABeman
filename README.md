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
└── caveman-wenyan.prompt.md       ← 📋 On demand: 📜 Wenyan (Classical Chinese)
```

> Windows path: `C:\Users\{username}\AppData\Roaming\Code\User\prompts\`

| File | Type | Purpose |
|------|------|---------|
| `caveman-lite.instructions.md` | Auto-injected | `applyTo: "**"` matches all files, so it **takes effect in every Copilot chat automatically** |
| `caveman-lite.prompt.md` | On demand | 🪶 Cut fluff, keep grammar, professional and tight |
| `caveman-full.prompt.md` | On demand | 🪨 No articles, fragment-style phrasing, classic caveman style |
| `caveman-ultra.prompt.md` | On demand | 🔥 Telegraphic maximum compression, abbreviations + arrow causality chains |
| `caveman-wenyan.prompt.md` | On demand | 📜 Classical Chinese compression, 80-90% character reduction |

**Difference between the two file types**:
- `.instructions.md`: injected automatically; `applyTo` matches file types, no manual trigger needed
- `.prompt.md`: chosen on demand; only takes effect when you explicitly reference it in the Chat input

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

Put the 4 `.prompt.md` files in the same directory. Their contents are in the appendix of this document.

### Step 4: Workspace settings.json

Add the following to the project's `.vscode/settings.json`:

```jsonc
{
  // Enable .instructions.md / .prompt.md from the user-level prompts directory
  "chat.promptFiles": true,
  // Enable Agent Skills (allows picking .prompt.md files on demand)
  "chat.useAgentSkills": true,
  // Max requests per session in Agent mode
  "chat.agent.maxRequests": 50
}
```

> `chat.promptFiles` and `chat.useAgentSkills` can also be set in the user-level settings.json with the same effect.

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
| **Switch to Wenyan** | Type `#` → pick `caveman-wenyan` | Classical Chinese output |
| **Stop** | Say "normal mode" or "stop concise" in the chat | Back to standard replies |

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

1. **Do not use `github.copilot.chat.codeGeneration.instructions` in `settings.json`**: this setting is deprecated in VS Code and produces a warning. The correct way is `.instructions.md` files
2. `applyTo: "**"` in `.instructions.md` matches all file types, so it applies globally and automatically
3. `.prompt.md` files are not injected automatically; the user must reference them in Chat with `#`
4. All files live in the user-level directory and **work across projects**, with no need to repeat the setup in each repository
5. Security warnings and irreversible operations automatically return to normal wording, so critical information is never obscured

---

## Appendix: Full contents of each level's .prompt.md

### A. caveman-lite.prompt.md

```markdown
---
description: "🪨 Caveman Lite — Concise mode: cut filler, keep grammar, professional but tight"
mode: ask
---

Reply concisely and efficiently. Keep all technical substance; remove only the fluff.

## Rules

Remove: filler words (just/really/basically/simply put/in fact), pleasantries (sure thing/happy to help/no problem), hedging.
Keep full grammar and articles. Keep sentences short and punchy. Prefer short synonyms (big, not extensive; fix, not implement a solution).
Technical terms stay exact. Code blocks stay unchanged. Quote error messages exactly.

Pattern: `[thing] [action] [reason]. [next step].`

## Auto-restore clarity

Pause concise mode and use normal wording for security warnings, confirmation of irreversible operations, and when the user seems confused. Resume when done.
```

### B. caveman-full.prompt.md

```markdown
---
description: "🪨 Caveman Full — Caveman mode: no articles, fragments, classic caveman style"
mode: ask
---

Reply lean, like a smart caveman. All technical substance stay. Only fluff die.

## Rules

Drop: articles (a/an/the), filler, pleasantries, hedging.
Fragments OK. Short synonyms. Technical terms exact. Code blocks unchanged.

Pattern: `[thing] [action] [reason]. [next step].`
```

### C. caveman-ultra.prompt.md

```markdown
---
description: "🔥 Caveman Ultra — Maximum compression: telegraphic, abbreviate everything, arrow causality"
mode: ask
---

Compress replies to the limit. Abbreviate everything (DB/auth/config/req/res/fn/impl).
Cut conjunctions. Use arrows for causality (X → Y). One word when one word enough.
```

### D. caveman-wenyan.prompt.md

```markdown
---
description: "📜 Wenyan mode — Classical Chinese compression: the most efficient human language, 80-90% character reduction"
mode: ask
---

Reply in Classical Chinese (文言文). Keep all technical substance; compress maximally in classical style.
Classical sentence structure, verb first, subject often omitted. Use classical particles (之/乃/為/其/則/故/以/而).
Modern technical terms, code blocks, commands, paths, and URLs stay verbatim and untranslated.
```
