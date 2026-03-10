# Unitask Agent Skill Changelog

## [Unreleased]
### Added

### Changed

### Fixed

### Security

## [0.18.1] - 2026-03-10
### Added

### Changed
- Aligned the hosted and stdio MCP server metadata to report the current `unitask-agent` release line.
- Repackaged the canonical `unitask-agent` skill bundle for GitHub and ClawHub so published files match the local source-of-truth package.

### Fixed
- Corrected online distribution drift where ClawHub `0.18.0` files lagged behind the local and GitHub skill package contents.

### Security

## [0.18.0] - 2026-02-23
### Added
- Expanded the published skill package coverage to include richer real-world prompt/output examples and setup guidance for modern agent runtimes.

### Changed
- Updated onboarding flow to explicitly support Codex IDE and added a recommended one-time timezone setup so planning queries behave correctly by default.
- Refined skill docs for clearer tool usage, safer destructive-action handling, and more predictable day-planning behavior.

### Fixed
- Clarified default timezone behavior for task-list queries when `tz` is omitted, reducing setup confusion in multi-client environments.

### Security
- Reinforced least-privilege token usage and explicit confirmation patterns for destructive actions in skill guidance.

## [0.17.1] - 2026-02-18
### Added
- Published `unitask-agent` package line with onboarding, use cases, and examples for OpenClaw/Codex/Claude runtimes.

### Changed
- Aligned skill metadata and distribution docs with `unitask-agent` as the canonical slug.

### Fixed
- Clarified timezone default behavior and retrieval controls in skill docs to reduce setup confusion.

### Security
- Reinforced least-privilege token scope guidance (`read`, `write`, `delete`) in skill onboarding.
