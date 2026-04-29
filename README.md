# SuperCaveManMode

> Clarity and brevity are not opposites.

A Claude Code skill that strips filler from assistant output without touching workflow discipline. Cuts ~65–75% of output tokens while keeping technical precision, structure, and safety-critical messages intact.

Sits naturally on top of [obra/superpowers](https://github.com/obra/superpowers): brainstorming still asks one question at a time, TDD still goes red→green, debugging still follows the process — just without the throat-clearing.

Inspired by [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) and [obra/superpowers](https://github.com/obra/superpowers).

---

## How it works

Most assistant tokens are filler: pleasantries, hedges, restatements, trailing summaries, and articles that carry no information. SuperCaveManMode is a style overlay that removes them on the way out, while preserving everything that matters — code, file paths, structure, plans, and safety warnings.

It does **not** fork or modify any other skill. It layers on top.

---

## SuperCaveManMode vs. Superpowers

They solve different problems and they are designed to run together.

| | Superpowers | SuperCaveManMode |
|---|---|---|
| **What it does** | Gives the agent a disciplined workflow (brainstorm → plan → TDD → review → finish) | Compresses the agent's prose output |
| **Acts on** | The agent's *behavior* — what steps to take, in what order | The agent's *phrasing* — how it writes between steps |
| **Modifies code?** | No | No |
| **Modifies plans / specs / commit messages?** | No | **No** |
| **Without the other** | Works fully on its own | Works on its own, but the leverage is bigger when paired |

Superpowers makes the agent do the right thing. SuperCaveManMode makes it stop padding the explanation while it does it.

---

## How plans, specs, and critical info stay intact

Compression is a phrasing layer over chat output. It is fenced off from anything load-bearing:

1. **File contents are off-limits.** Plans (`docs/.../plans/*.md`), specs, code, comments, commit messages, PR descriptions, `CLAUDE.md`, and memory files are written by `Write`/`Edit` tools — those bytes are never touched by this skill. A plan written under SuperCaveManMode looks identical to a plan written without it.
2. **Workflow discipline is non-negotiable.** Compression applies to *how* the agent writes, not to *whether* it follows the workflow. Brainstorming still asks one question at a time. TDD still writes the failing test first. Plans still get checkpoints. The skill explicitly forbids skipping required steps "to save tokens."
3. **Safety output reverts to full sentences automatically.** Destructive ops, security warnings, and risk confirmations bypass compression entirely — including the reasoning, blast radius, and confirmation prompt — so nothing important gets clipped at the moment it matters.
4. **Concrete referents are preserved.** `auth.py:42` never becomes "it." Function names, flags, error codes, and `file:line` references are kept verbatim. Compression drops filler, not anchors.
5. **Code blocks pass through untouched.** Diffs, snippets, command lines — all rendered exactly as the agent would render them without the skill.
6. **Explicit on/off.** Activates only when invoked. `normal mode` or `verbose` switches it off for the rest of the session. No magic phrases auto-enable it.

If at any point the compression is hiding information you need, say `verbose` and the next response will be in full prose.

---

## Install

### Claude Code (recommended, no clone)

```text
/plugin marketplace add marc1277/supercavemanmode
/plugin install supercavemanmode@supercavemanmode
```

Restart the session if the skill doesn't appear immediately.

### Manual / development

```bash
git clone https://github.com/marc1277/supercavemanmode
```

Point Claude Code at the local path, or copy the folder into `~/.claude/plugins/`.

### Verify

Start a new session and invoke:

```text
/skill supercavemanmode
```

The next assistant turn should be visibly tighter — no "Great question!", no "Let me…", no trailing summary.

---

## Usage

- **Activate:** invoke the skill explicitly. Stays on for the rest of the session.
- **Deactivate:** say `normal mode` or `verbose`.
- **Pairs well with:** [superpowers](https://github.com/obra/superpowers). Works on its own; combined leverage is the point.

---

## What gets dropped

- Articles (`a` / `an` / `the`) where meaning is preserved
- Pleasantries (`Great question!`, `Sure`, `Happy to help`)
- Hedges (`I think`, `perhaps`, `it seems like`, `maybe`)
- Restatements of your question
- Trailing summaries of what was just done
- Throat-clearing preambles (`Let me…`, `I'll go ahead and…`, `First, I'll…`)

## What stays untouched

- Technical terms verbatim — function names, file paths, flags, error codes
- Code blocks — never compressed
- Lists, headers, structural markers, A/B/C options
- `file:line` references
- Concrete referents (no replacing `auth.py:42` with `it`)

**Rule of thumb:** if removing a word loses zero information, remove it. If it loses precision, keep it.

---

## Examples

**Before:**
> "Great question! I'll go ahead and read the config file first to see what's currently set, and then I'll update the relevant field. Let me know if that sounds good!"

**After:**
> "Reading config, updating field."

**Before:**
> "I think the bug is probably in the authentication handler at auth.py line 42 — it seems like the token validation might be failing silently when the header is missing."

**After:**
> "Bug in auth.py:42 — token validation fails silently when header missing."

---

## Safety

Compression auto-disables — and full sentences resume — for output that must be unambiguous:

- Destructive operations: `rm -rf`, `git push --force`, `git reset --hard`, branch deletion, dropping tables
- Security warnings and prompt-injection flags
- Confirmation prompts before risky or irreversible actions
- Critical error diagnostics where ambiguity could cause harm

Compression resumes after the safety-sensitive span ends.

---

## Scope

Affects assistant **text output only**. Does not touch:

- Generated code or code comments
- Commit messages or PR descriptions
- File contents written via Write/Edit
- Memory files or `CLAUDE.md`

---

## Superpowers interop

- All superpowers skills still trigger normally.
- Workflow structure is unchanged — only phrasing is compressed.
- Checklists are preserved as-is (already information-dense).
- Compression never skips a required workflow step. Discipline > tokens.

---

## Updating

```text
/plugin update supercavemanmode
```

---

## Contributing

Issues and PRs welcome at <https://github.com/marc1277/supercavemanmode>.

For new skills or substantial behavior changes, open an issue first to discuss.

---

## License

[MIT](LICENSE)
