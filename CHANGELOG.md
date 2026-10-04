# Changelog

## v1.5.0 (2026-10-04)

- **skill is just the analysis now**: SKILL.md drops the bash preamble and epilogue, version checks, auto-update, telemetry, self-rating, field reports and "did clearshot nail it?" prompts. ~60% fewer words loaded on every screenshot, and no shell commands run before an answer
- **no telemetry**: the skill itself makes no network calls; setup no longer asks the two onboarding questions or writes `~/.clearshot/`; update by re-running the reinstall command in the README
- **sharper gate**: a UI screenshot shared only for what it says (a message, an error) gets a normal answer, not a full analysis
- removed the now-unused `bin/onboarding.sh`, `bin/update-prompt.sh`, `bin/telemetry-setup.sh`, `bin/picker.sh` and `config.sh`

## v1.4.0 (2026-03-25)

- **install simplification**: git clone is now the primary install method — paste one command into claude code
- **non-interactive setup**: setup script detects when running without a TTY and uses defaults — works when claude runs it
- **self-healing trigger**: setup now updates the CLAUDE.md trigger block in-place when it's outdated
- **safer self-repair**: repair command only removes symlinks (not directories), uses git clone instead of curl
- **slimmer npm package**: convex moved to devDependencies, ships only what's needed

## 1.3.0 (2026-03-25)

- Replaced 5-step pipeline with 3 analysis levels: Map, System, Blueprint
- Levels 1+2 always run; Level 3 escalates when building from screenshot
- Unified facts+taste analysis — removed separate analytical/qualitative/blended modes
- 5×5 spatial grid (was 3×3) for more granular layout mapping
- README restructured: install first, clean copy-friendly code blocks
- Path-agnostic preamble: works from ~/.claude/skills/ or ~/.agents/skills/
- No network calls until user completes first-run onboarding
- Combined onboarding script (bin/onboarding.sh) for any install method

## 1.2.0 (2026-03-24)

- Interactive onboarding: arrow-key pickers for update mode and telemetry during setup
- Two update modes: "always keep updated" (auto-pull) or "ask me every time"
- Simplified telemetry to two options: anonymous / off (removed confusing three-tier system)
- Interactive update prompt when new version detected (for "ask" mode users)
- Shared picker component (`bin/picker.sh`) used across all interactive prompts

## 1.1.0 (2026-03-24)

- Guaranteed trigger: setup now injects a BLOCKING REQUIREMENT into ~/.claude/CLAUDE.md
- Robust installer: handles broken symlinks, network failures, missing git
- Removed bloat: convex plugin manifest, package-lock, auto-generated files
- Symlink verification: setup now checks target matches actual repo location

## 1.0.0 (2026-03-24)

- Initial release: Clearshot — structured screenshot analysis skill for Claude Code
- 5-step analysis pipeline: spatial decomposition, element inventory, design tokens, layout architecture, interaction map
- Gate check with early exit for non-UI images
- Dual mode: analytical (engineer lens) + qualitative (designer lens)
- Dynamic step execution based on conversation context
- Auto-update checking via GitHub VERSION file
- Telemetry with 2 modes (anonymous / off)
- Self-rating protocol with field report generation
- Convex backend for telemetry aggregation
