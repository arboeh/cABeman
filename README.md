🇬🇧 **English** | [🇩🇪 Deutsch](README.de.md)

<img src="images/heading.svg" alt="huABus" height="40"/>

Token-saving prompt files for **Claude Code** and **GitHub Copilot Chat**. Based on [caveman](https://github.com/JuliusBrussee/caveman): constrain the output style, cut filler, keep all technical substance.

A small **always-on** rule (Micro) plus **on-demand levels** for when you want it terser:

| Level | Style |
|-------|-------|
| 🤏 **Micro** (default, always on) | Six-line ruleset: no articles/filler/pleasantries, fragments fine, technical terms exact |
| 🪶 **Lite** | Cut fluff, keep grammar, professional and tight |
| 🪨 **Full** | No articles, fragments, classic caveman style |
| 🔥 **Ultra** | Telegraphic, abbreviations, arrow causality chains |

## Repository layout

```
rules/caveman.md                       Claude Code, always on (Micro)
skills/caveman-{lite,full,ultra}/      Claude Code, on demand (/caveman-full ...)
copilot/prompts/                       Copilot Chat: caveman-micro.instructions.md + 3 .prompt.md levels
docs/                                  Detailed setup guides
```

## Install

**Claude Code** (clone or download this repo, run inside it):

```bash
mkdir -p ~/.claude/rules ~/.claude/skills
cp rules/caveman.md ~/.claude/rules/
cp -r skills/* ~/.claude/skills/
```

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\rules","$HOME\.claude\skills" | Out-Null
Copy-Item .\rules\caveman.md "$HOME\.claude\rules\" -Force
Copy-Item .\skills\* "$HOME\.claude\skills\" -Recurse -Force
```

Start a new session: rules are read at launch.

**Copilot Chat:** copy `copilot/prompts/*` into your VS Code user `prompts` folder (Windows: `%APPDATA%\Code\User\prompts\`, macOS: `~/Library/Application Support/Code/User/prompts/`, Linux: `~/.config/Code/User/prompts/`).

More detail, skill-manager import, changing the default level: [docs/claude-code.md](docs/claude-code.md), [docs/copilot.md](docs/copilot.md).

## Usage

Micro applies automatically. Switch level by typing the slash command:

```
/caveman-full Explain how Vue's nextTick works
/caveman-ultra What performance problems does this code have?
```

Say "normal mode" or "stop concise" to go back to standard replies.

## Example

"Difference between Vue ref and reactive":

| Level | Reply |
|-------|-------|
| No caveman | "Of course! `ref` and `reactive` are both reactivity tools in Vue 3's Composition API. Simply put, `ref` is mainly used for primitive values..." |
| 🪶 Lite | "`ref` wraps primitives, access via `.value`. `reactive` is for objects, access properties directly." |
| 🪨 Full | "`ref` wrap primitive, need `.value`. `reactive` for object, direct access." |
| 🔥 Ultra | "`ref` → primitive + `.value`. `reactive` → obj, direct access." |

Samples are illustrative, not measured. The "~65 % fewer output tokens" figure of the original guide was not measured here either.

## Notes

- Security warnings and irreversible operations return to normal wording, so critical information is never obscured.
- The rules are written for English replies; other languages carry the style over less precisely.

## Credits

- [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman): the caveman style.
- [kuba-guzik/caveman-micro](https://github.com/kuba-guzik/caveman-micro) (MIT): the Micro ruleset.
- [birght/lesstokens_for_vscode](https://github.com/birght/lesstokens_for_vscode): original Copilot Chat prompt files (Lite, Full, Ultra), translated here. That repository has no license file.

## License

[MIT](LICENSE) for the original contents of this repository. Third-party material and its licenses: [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).
