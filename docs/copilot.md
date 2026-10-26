# Copilot Chat setup

Files go into the **VS Code user-level `prompts` folder**. They work across projects and do not touch any repository.

| OS | Path |
|----|------|
| Windows | `%APPDATA%\Code\User\prompts\` |
| macOS | `$HOME/Library/Application Support/Code/User/prompts/` |
| Linux | `$HOME/.config/Code/User/prompts/` |

The folder belongs to your VS Code **profile**; with several profiles, add the files to the one you use. Other editions (e.g. Insiders: `Code - Insiders`) use a different folder. Easiest way to find the right place: run **Chat: New Prompt File** and choose the user location.

## Files

| File | Type | Effect |
|------|------|--------|
| `caveman-micro.instructions.md` | Auto-injected | `applyTo: "**"` applies Micro to every chat |
| `caveman-lite.prompt.md` | On demand | `/caveman-lite` |
| `caveman-full.prompt.md` | On demand | `/caveman-full` |
| `caveman-ultra.prompt.md` | On demand | `/caveman-ultra` |

Only Micro is auto-injected: a file with `applyTo: "**"` hits every chat, so it must be low-risk, and several such files would be injected at once with no guaranteed winner.

## Usage

Type `/` in the chat input, pick the prompt, add your question. Alternatives: **Chat: Run Prompt**, or the play button on an open `.prompt.md`. (`#` adds context such as `#file:`; it does not run prompts.) A prompt applies to the chat you run it in; new chats fall back to Micro. If a user-level prompt is missing from the `/` list, use **Chat: Run Prompt**.

## Change default or scope

- Replace the body of `caveman-micro.instructions.md` with another level and rename the file.
- Narrow the scope, e.g. `applyTo: "src/**"`.
- Single project: put the rules in `.github/copilot-instructions.md`.

## Optional setting

Agent mode request limit (default 25):

```jsonc
{ "chat.agent.maxRequests": 50 }
```
