# Agent guide

This file is the map for agents working in this repository. It has two jobs: it sets the
response style every agent should use here, and it points to the code.

This is a fork of [TauricResearch/TradingAgents](https://github.com/TauricResearch/TradingAgents).
Changes that belong upstream should be proposed upstream, not carried indefinitely in this fork.

## Output style

The reader has ADHD. Shape every response so it can be acted on:

1. Lead with the answer or next action: command, path, or snippet first.
2. Number multi-step work; one bounded action per step.
3. End with one next action doable in under two minutes.
4. Finish the current issue before raising a new one.
5. Restate progress each turn ("step 3 of 5 done").
6. Give time estimates in concrete units, never "a bit".
7. After a change, show what now works.
8. Errors: state location, cause, and fix. No drama.
9. Rank and group long lists; aim for at most five items per group without omitting relevant items.
10. No preamble, no recaps, no closers.

Exceptions: explain fully when asked to explain. Confirm before destructive actions. After three failed fixes, stop and name the doubtful assumption. If the request is ambiguous, ask one short question.

The full ruleset, with examples and the pre-send check, lives in
[`.claude/skills/i-have-adhd/SKILL.md`](.claude/skills/i-have-adhd/SKILL.md). That file is the
source of truth for the style; the ten lines above are the condensed version for runtimes that
read `AGENTS.md` but not `SKILL.md`.

## Repository map

| Area | Location | Purpose |
| --- | --- | --- |
| Core package | `tradingagents/` | Agents (`agents/`), data sources (`dataflows/`), the orchestration graph (`graph/`), LLM providers (`llm_clients/`), report rendering (`reporting.py`). |
| Defaults | `tradingagents/default_config.py` | Base config; env vars and CLI flags layer on top of it. |
| CLI | `cli/`, `main.py` | `cli/main.py` is the entry point; `cli/config.py` handles precedence. |
| Tests | `tests/` | pytest suite, one file per behavior. `tests/conftest.py` holds shared fixtures. |
| Packaging | `pyproject.toml`, `requirements.txt` | Dev extras install with `pip install -e ".[dev]"`. |
| Config and keys | `.env.example`, `.env.enterprise.example` | Copy to `.env`. Never commit real keys, and never read a populated `.env`. |
| Container | `Dockerfile`, `docker-compose.yml` | Containerized runs. |
| CI | `.github/workflows/ci.yml` | pytest on Python 3.10-3.13, a clean-install import smoke test, and strict ruff. |

## Verification

Run what CI runs, smallest first:

```bash
pip install -e ".[dev]"   # once per environment
pytest -q                 # full suite
pytest -q tests/test_signal_processing.py   # single file while iterating
ruff check .              # must be clean; CI lints the whole repo
```

Tests that reach a live vendor need API keys from `.env`. A failure caused by a missing key is
not a regression; say so rather than working around it.

## Keeping the style rules in sync

`SKILL.md` is vendored from [ayghri/i-have-adhd](https://github.com/ayghri/i-have-adhd) (v0.3.0).
To pull a newer version, copy it from a checkout of that repo:

```bash
cp skills/i-have-adhd/SKILL.md <this-repo>/.claude/skills/i-have-adhd/SKILL.md
cp skills/i-have-adhd/SKILL.md <this-repo>/.cursor/skills/i-have-adhd/SKILL.md
```

Both copies must stay byte-identical to the source; `diff` them after copying. Edit the upstream
skill rather than diverging here.
