# screen-to-answers

A portable agent skill that lets an agent read the user's screen, solve the questions/problems visible on it (homework, quizzes, forms, error messages), and present the answers with the working shown.

## What's in the app

- `screen-answers/SKILL.md` — the skill itself (single file, no dependencies beyond the capture library):
  - **Setup** — one-line install (`python -m pip install pyscreeze`), macOS Screen Recording permission, and platform fallbacks (macOS `screencapture`, PowerShell .NET) for agents without Python.
  - **Workflow** — bring the relevant window to front → capture the full screen to a unique file → read the image (multimodal) → solve with visible working → never click Submit unless asked → re-capture on "next".
  - **Pitfalls** — six hard-won lessons: screenshot tools that return null through code-execution layers, resolution scaling (detect coordinates programmatically, don't eyeball them), UIA not seeing browser DOM, flaky native `<select>` dropdowns, no `setTimeout` in execute runtimes, verify after the first field.

## Installation locations

The skill is a folder containing `SKILL.md`. Copy the `screen-answers/` folder into the target agent's skills directory:

| Agent | Project scope | Global (per user) |
|---|---|---|
| OpenCode | `.opencode/skills/screen-answers/` | `~/.config/opencode/skills/screen-answers/` |
| OpenCode (compat) | `.claude/skills/`, `.agents/skills/` | `~/.claude/skills/`, `~/.agents/skills/` |
| Claude Code | `.claude/skills/screen-answers/` | `~/.claude/skills/screen-answers/` |

Requirements: Python 3.10+ and `python -m pip install pyscreeze`. On macOS, grant the terminal *Screen Recording* permission (System Settings → Privacy & Security → Screen Recording).

## Compatibility

- **Windows: compatible and working (verified).**
- macOS / Linux: use the same pyscreeze path (macOS needs the Screen Recording permission); not verified in this release.

## Usage

After installation the skill is discovered automatically. Trigger it with prompts like "check my screen" or "answer the questions on it", or load it explicitly by ID (`screen-answers`) / `@screen-answers`.
