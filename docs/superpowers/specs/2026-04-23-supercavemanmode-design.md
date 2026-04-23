# SuperCaveManMode — Design Spec

Date: 2026-04-23
Author: marc-andre@bamboosoft.ca
Status: Approved for implementation planning

## Summary

A publishable Claude Code skill that combines two existing skills:
- **Caveman** (JuliusBrussee/caveman): telegraphic response style, ~65–75% token savings
- **Superpowers** (anthropics official plugin): disciplined workflow skills (brainstorming, TDD, debugging, plans)

SuperCaveManMode is a **style overlay**. It does not fork or re-implement either source skill. It layers compression rules and safety overrides on top of whatever superpowers skills are already installed.

## Goals

- Cut assistant output tokens without losing information.
- Preserve superpowers discipline: workflows still run (brainstorming still asks questions, TDD still writes tests first, debugging still follows the process).
- Safe by default: compression auto-disables for destructive ops and security warnings.

## Non-goals

- Not a fork of superpowers. Upstream changes to superpowers skills continue to work.
- Not a modifier of generated code, comments, or commit messages. Only the assistant's own text output.
- Not a general memory compressor. (Caveman's CLAUDE.md compression tool is out of scope here.)

## Core principle

**Clarity and brevity are not opposites.** Drop filler; keep information. If removing a word loses zero information, remove it. If removing it loses precision, keep it.

## Behavior

### Activation

- Skill invoked → always-on.
- Stays active until user says "normal mode" or "verbose".
- No auto-trigger from other phrases. Explicit in, explicit out.

### Compression rules

**Drop:**
- Articles (a / an / the) where meaning is preserved.
- Pleasantries ("Great question!", "Sure thing", "Happy to help").
- Hedges ("I think", "perhaps", "it seems like").
- Restatements of the user's question.
- Trailing summaries of what was just done.
- Throat-clearing preambles ("Let me...", "I'll go ahead and...").

**Keep:**
- Technical terms verbatim (function names, file paths, flags, error codes).
- Code blocks untouched.
- Structural markers: lists, section headers, A/B/C options.
- `file:line` references.
- Concrete referents (don't replace "the login handler at auth.py:42" with "it").

### Superpowers interop

- Still invokes superpowers skills per their normal triggers.
- Brainstorming still asks one question at a time, still uses multiple choice, still presents design sections — all stripped of filler.
- TDD, debugging, writing-plans workflows unchanged in structure; outputs compressed.
- Checklists preserved (they're information-dense already).

### Safety auto-disable

Compression temporarily disabled — resume after — for:
- Destructive operations (rm -rf, force push, hard reset, branch deletion).
- Security warnings and prompt-injection flags.
- Confirmation prompts before risky actions.
- Critical error diagnostics where ambiguity could cause harm.

During a safety-disabled span, the assistant writes in full sentences and includes the reason clearly.

### Scope of effect

- Assistant text output only.
- Does not modify: generated code, code comments, commit messages, PR descriptions, file contents, memory files, CLAUDE.md.

## Repository layout

```
supercavemanmode/
├── README.md
├── LICENSE
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── supercavemanmode/
        └── SKILL.md
```

- `SKILL.md`: frontmatter (name, description) + the behavior rules above, written as a directive to the assistant.
- `plugin.json`: Claude Code plugin manifest for marketplace installation.
- `README.md`: what it does, install instructions, activation/deactivation phrases, safety notes.
- `LICENSE`: MIT (matches caveman upstream).

## Distribution

- Published as a standalone GitHub repo.
- Installable via Claude Code plugin marketplace conventions (same pattern as JuliusBrussee/caveman).
- Requires superpowers plugin to be installed separately (documented in README as a prerequisite for full value, though the style rules work without it).

## Open items for the plan

- Exact wording of SKILL.md frontmatter `description` (must fairly trigger, not over-trigger).
- Exact plugin.json schema (verify against current Claude Code plugin spec).
- Whether to include a short "examples" section in SKILL.md showing before/after compression on realistic outputs.
- README structure and install commands.
