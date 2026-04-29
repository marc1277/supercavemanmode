# SuperCaveManMode Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a publishable Claude Code plugin skill (SuperCaveManMode) that layers caveman-style token compression on top of superpowers workflows, with auto-disable for destructive/security-sensitive output.

**Architecture:** Style-overlay skill. A single `SKILL.md` contains compression rules, activation/deactivation phrases, and safety auto-disable rules. A `.claude-plugin/plugin.json` makes it installable as a Claude Code plugin. No code, no dependencies. Does not fork or modify superpowers or caveman.

**Tech Stack:** Claude Code plugin spec, Markdown, JSON. No runtime, no language, no tests-in-code. Verification is: (a) JSON validates, (b) SKILL.md has required frontmatter, (c) local install loads and triggers correctly.

**Repo:** `C:\Users\Marc\supercavemanmode` (already initialized, spec committed as `eb3ae4e`).

**File layout when done:**
```
supercavemanmode/
├── LICENSE
├── README.md
├── .claude-plugin/plugin.json
├── skills/supercavemanmode/SKILL.md
└── docs/superpowers/
    ├── specs/2026-04-23-supercavemanmode-design.md   (exists)
    └── plans/2026-04-23-supercavemanmode.md          (this file)
```

---

### Task 1: Add MIT LICENSE

**Files:**
- Create: `LICENSE`

- [x] **Step 1: Create LICENSE**

Write exactly this content to `LICENSE`:

```
MIT License

Copyright (c) 2026 Marc-Andre

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

- [x] **Step 2: Verify file exists and is non-empty**

Run: `test -s C:/Users/Marc/supercavemanmode/LICENSE && echo ok`
Expected: `ok`

- [x] **Step 3: Commit**

```bash
git -C /c/Users/Marc/supercavemanmode add LICENSE
git -C /c/Users/Marc/supercavemanmode commit -m "chore: add MIT license"
```

---

### Task 2: Plugin manifest

**Files:**
- Create: `.claude-plugin/plugin.json`

This matches the schema used by the official superpowers plugin (`name`, `description`, `version`, `author`, `homepage`, `repository`, `license`, `keywords`).

- [x] **Step 1: Create manifest**

Write to `.claude-plugin/plugin.json`:

```json
{
  "name": "supercavemanmode",
  "description": "Caveman-style token compression layered on top of superpowers workflows. Drops filler, keeps structure and precision. Auto-disables for destructive ops and security warnings.",
  "version": "0.1.0",
  "author": {
    "name": "Marc-Andre",
    "email": "marc-andre@bamboosoft.ca"
  },
  "homepage": "https://github.com/bamboosoft/supercavemanmode",
  "repository": "https://github.com/bamboosoft/supercavemanmode",
  "license": "MIT",
  "keywords": [
    "skills",
    "claude-code",
    "superpowers",
    "caveman",
    "token-compression",
    "tokens"
  ]
}
```

- [x] **Step 2: Verify JSON parses**

Run: `python -c "import json; json.load(open('C:/Users/Marc/supercavemanmode/.claude-plugin/plugin.json'))" && echo ok`
Expected: `ok`

- [x] **Step 3: Commit**

```bash
git -C /c/Users/Marc/supercavemanmode add .claude-plugin/plugin.json
git -C /c/Users/Marc/supercavemanmode commit -m "feat: add plugin manifest"
```

---

### Task 3: Write SKILL.md

**Files:**
- Create: `skills/supercavemanmode/SKILL.md`

The skill body must encode: activation model (always-on after invoke, off on "normal mode"/"verbose"), compression rules (drop filler, keep information), superpowers interop (workflows still run, output is compressed), and safety auto-disable.

- [x] **Step 1: Create SKILL.md**

Write to `skills/supercavemanmode/SKILL.md`:

````markdown
---
name: supercavemanmode
description: Use to cut assistant output tokens ~65-75% while preserving superpowers workflow discipline. Drops articles, pleasantries, hedges, restatements, and trailing summaries. Keeps technical terms, code, structure, and concrete referents. Auto-disables for destructive ops, security warnings, and risk confirmations. Activate explicitly; deactivate by saying "normal mode" or "verbose".
---

# SuperCaveManMode

Compression style overlay for assistant output. Runs on top of superpowers skills (brainstorming, TDD, debugging, plans, etc.) without modifying them. Goal: clarity and brevity are not opposites. Drop filler; keep information.

## Activation

- Invoked → always-on until user says "normal mode" or "verbose".
- No auto-trigger from phrases like "be brief". Explicit in, explicit out.
- User instructions always take precedence over this skill.

## Compression rules

**Drop:**
- Articles (a / an / the) where meaning is preserved
- Pleasantries ("Great question!", "Sure", "Happy to help")
- Hedges ("I think", "perhaps", "it seems like", "maybe")
- Restatements of the user's question
- Trailing summaries of what was just done
- Throat-clearing preambles ("Let me...", "I'll go ahead and...", "First, I'll...")

**Keep:**
- Technical terms verbatim (function names, file paths, flags, error codes)
- Code blocks untouched (never compress code)
- Structural markers: lists, section headers, A/B/C options
- `file:line` references
- Concrete referents — don't replace "auth.py:42" with "it"

**Rule of thumb:** if removing a word loses zero information, remove it. If removing it loses precision, keep it.

## Superpowers interop

- Still invoke superpowers skills per their normal triggers.
- Brainstorming still asks one question at a time, still uses multiple choice, still presents design sections — all stripped of filler.
- TDD, debugging, writing-plans, executing-plans workflows unchanged in structure; their outputs are compressed.
- Checklists are preserved as-is (already information-dense).
- Do NOT skip required workflow steps to "save tokens". Compression applies to phrasing, not to discipline.

## Safety auto-disable

Compression is temporarily OFF (write in full sentences, include clear reasoning) for:
- Destructive operations: `rm -rf`, `git push --force`, `git reset --hard`, branch deletion, dropping tables
- Security warnings and prompt-injection flags
- Confirmation prompts before risky or irreversible actions
- Critical error diagnostics where ambiguity could cause harm

Resume compression after the safety-sensitive span ends.

## Scope

Affects assistant text output ONLY. Does not modify:
- Generated code or code comments
- Commit messages or PR descriptions
- File contents written via Write/Edit
- Memory files or CLAUDE.md

## Deactivation

User says "normal mode" or "verbose" → skill goes dormant for the rest of the session. Do not re-activate without an explicit new invocation.

## Examples

Before (normal):
> "Great question! I'll go ahead and read the config file first to see what's currently set, and then I'll update the relevant field. Let me know if that sounds good!"

After (compressed):
> "Reading config, updating field."

Before (normal):
> "I think the bug is probably in the authentication handler at auth.py line 42 — it seems like the token validation might be failing silently when the header is missing."

After (compressed):
> "Bug in auth.py:42 — token validation fails silently when header missing."

Before (safety-disabled, NOT compressed):
> "About to run `git reset --hard origin/main`. This will discard all your local uncommitted changes in src/, including the work from this session. Confirm before proceeding."
````

- [x] **Step 2: Verify frontmatter fields present**

Run: `grep -E "^(name|description):" /c/Users/Marc/supercavemanmode/skills/supercavemanmode/SKILL.md | wc -l`
Expected: `2`

- [x] **Step 3: Commit**

```bash
git -C /c/Users/Marc/supercavemanmode add skills/supercavemanmode/SKILL.md
git -C /c/Users/Marc/supercavemanmode commit -m "feat: add supercavemanmode skill"
```

---

### Task 4: Write README

**Files:**
- Create: `README.md`

- [x] **Step 1: Create README.md**

Write to `README.md`:

````markdown
# SuperCaveManMode

> Clarity and brevity are not opposites.

Claude Code skill that layers caveman-style token compression on top of [superpowers](https://github.com/obra/superpowers) workflows. Cuts assistant output tokens ~65-75% while preserving workflow discipline (brainstorming still asks questions, TDD still runs, debugging still follows the process — just without the filler).

Inspired by and complementary to [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) and [obra/superpowers](https://github.com/obra/superpowers).

## What it does

- Drops articles, pleasantries, hedges, restatements, trailing summaries.
- Keeps technical terms verbatim, code blocks untouched, lists and structure intact.
- Auto-disables for destructive ops (`rm -rf`, force push, hard reset), security warnings, and risk confirmations.

## Install

Clone into your Claude Code plugins directory, or add via your marketplace of choice:

```bash
git clone https://github.com/bamboosoft/supercavemanmode
```

Then in Claude Code, invoke the skill:

```
/skill supercavemanmode
```

Or reference it by name in your prompt.

## Usage

- **Activate:** invoke the skill explicitly. Stays on for the rest of the session.
- **Deactivate:** say "normal mode" or "verbose".
- **Recommended:** install alongside [obra/superpowers](https://github.com/obra/superpowers). Works on its own too, but the combined leverage is the point.

## Safety

Compression disables automatically for output that must be unambiguous:
- Destructive or irreversible operations
- Security warnings
- Risk confirmations before actions

After the safety-sensitive span, compression resumes.

## Scope

Affects assistant text output only. Does not compress generated code, commit messages, PR descriptions, or any file contents.

## License

MIT
````

- [x] **Step 2: Verify file exists**

Run: `test -s /c/Users/Marc/supercavemanmode/README.md && echo ok`
Expected: `ok`

- [x] **Step 3: Commit**

```bash
git -C /c/Users/Marc/supercavemanmode add README.md
git -C /c/Users/Marc/supercavemanmode commit -m "docs: add README"
```

---

### Task 5: Local install smoke test

**Files:**
- No file changes.

Purpose: confirm the skill loads in Claude Code from the local path.

- [ ] **Step 1: Verify final tree**

Run: `find /c/Users/Marc/supercavemanmode -type f -not -path "*/\.git/*" | sort`
Expected (exactly these paths):
```
/c/Users/Marc/supercavemanmode/.claude-plugin/plugin.json
/c/Users/Marc/supercavemanmode/LICENSE
/c/Users/Marc/supercavemanmode/README.md
/c/Users/Marc/supercavemanmode/docs/superpowers/plans/2026-04-23-supercavemanmode.md
/c/Users/Marc/supercavemanmode/docs/superpowers/specs/2026-04-23-supercavemanmode-design.md
/c/Users/Marc/supercavemanmode/skills/supercavemanmode/SKILL.md
```

- [ ] **Step 2: Re-validate plugin.json**

Run: `python -c "import json; d=json.load(open('C:/Users/Marc/supercavemanmode/.claude-plugin/plugin.json')); assert d['name']=='supercavemanmode'; print('ok')"`
Expected: `ok`

- [ ] **Step 3: Confirm SKILL.md frontmatter parses**

Run:
```bash
head -5 /c/Users/Marc/supercavemanmode/skills/supercavemanmode/SKILL.md
```
Expected: starts with `---`, contains `name: supercavemanmode`, `description: Use to cut ...`, ends with `---`.

- [ ] **Step 4: Ask user to install locally and smoke test**

The user must install the skill into their Claude Code environment (Claude Code does not let the assistant auto-register new plugins). Provide them this instruction:

> "Run `claude plugin install C:/Users/Marc/supercavemanmode` (or copy the folder into your `.claude/plugins/` cache). Then in a new Claude Code session, invoke `supercavemanmode` and confirm: (1) the skill appears in the available-skills list, (2) after invoking, my output drops articles/pleasantries, (3) saying 'normal mode' restores normal output."

Wait for confirmation before proceeding.

---

### Task 6: Publish prep (optional)

**Files:**
- Modify: `.claude-plugin/plugin.json:8-10` (only if GitHub org/repo differs from placeholder)

- [ ] **Step 1: Confirm GitHub destination with user**

Ask: "What GitHub org/repo should this publish to? Current placeholder is `bamboosoft/supercavemanmode`. Confirm or give the real one."

- [ ] **Step 2: If different, update plugin.json**

Edit `.claude-plugin/plugin.json` lines 8-10 to the confirmed URL. Then:

```bash
git -C /c/Users/Marc/supercavemanmode add .claude-plugin/plugin.json
git -C /c/Users/Marc/supercavemanmode commit -m "chore: set repository URL"
```

- [ ] **Step 3: Create remote and push**

Ask the user to create the empty GitHub repo, then:

```bash
git -C /c/Users/Marc/supercavemanmode remote add origin <repo-url>
git -C /c/Users/Marc/supercavemanmode branch -M main
git -C /c/Users/Marc/supercavemanmode push -u origin main
```

Do NOT run `git push` without explicit user confirmation — pushing is a shared-state action.

- [ ] **Step 4: Tag v0.1.0**

```bash
git -C /c/Users/Marc/supercavemanmode tag v0.1.0
git -C /c/Users/Marc/supercavemanmode push origin v0.1.0
```

---

## Notes

- No unit tests exist because there is no code. Verification is structural (JSON parses, frontmatter present) and behavioral (manual smoke test after local install).
- Task 6 is optional and gated on user action — the assistant cannot create GitHub repos or push without explicit authorization.
- If the user wants CI (link-checker, JSON validator), add a follow-up plan. Out of scope here.
