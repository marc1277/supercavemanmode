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
