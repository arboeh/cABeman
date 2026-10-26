# Changelog

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versioning: [SemVer](https://semver.org/).

## [1.0.0] - 2026-10-26

First public release.

### Added
- Claude Code: always-on Micro rule (`rules/caveman.md`) and on-demand skills `caveman-lite`, `caveman-full`, `caveman-ultra`.
- Copilot Chat: auto-injected `caveman-micro.instructions.md` and on-demand prompt files for Lite, Full, Ultra (`copilot/prompts/`).
- German README, setup guides in `docs/`, MIT `LICENSE`, `THIRD-PARTY-NOTICES.md` (caveman: Apache-2.0, caveman-micro: MIT).
- Download package `cABeman-v1.0.0.zip` as release asset.

### Changed
- Prompt files translated from Chinese to English.
- README shortened from ~570 to ~85 lines; full file contents no longer duplicated in it.

### Removed
- `less_tokens.zip` from the repository (replaced by plain files and the release asset).

[1.0.0]: https://github.com/arboeh/cABeman/releases/tag/v1.0.0
