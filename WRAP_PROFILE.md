# Wrap profile: yahoo_football

Read by the personal wrap skills, `wrap-session-personal` and `wrap-terminal-personal`, whose
canonical copies live in the `personal` repo's `.claude/skills/` and are installed at the user
level by that repo's `tools/install-wrap-skills.ps1`. The skills are the procedure. This file is
what is particular to this repo. Fixed headings, in this order: About this repo, Extra steps,
Documentation sweep, Sensitivity check, The gate, The narrow gate, Commit rules, The pick-up
prompt. No branch name, model name, or machine path here, so derive them at run time.

## About this repo

Read-only Yahoo Fantasy Sports API tooling for one long-running fantasy football keeper league.
The goal is the historical record: an all-time record book, head to head rivalries,
luck-adjusted standings, and draft and trade retrospectives. Keeper valuation is the first
shipped feature rather than the point. This is the one **public** personal repo, and it holds
code only. The league's private artifacts, the draft-board screenshots, the keeper decision log
and other managers' rosters, live in `../personal/fantasy_football`.

**Currently blocked:** Yahoo gates the Fantasy Sports API behind a review and refuses this app
with `additional_authorization_required`. OAuth itself works and tokens are saved. Until access
is granted, every live call fails, so build and test offline.

## Extra steps

1. **Never run a live API call to check something.** `discover`, `pull` and `doctor --online` hit
   Yahoo, they cost something, and they currently fail anyway. Everything else is testable
   offline against stubs and fixtures.
2. **Use the venv interpreter explicitly** in every command, `\.venv\Scripts\python.exe`, since a
   bare `python` is a different install and will not have `yfpy`.
3. **Say whether the API block is still in force** in the wrap report, because it gates the whole
   roadmap and it is the first thing a fresh session needs to know.

## Documentation sweep

- **`README.md`**: the roadmap and the shipped-feature list, when either moved.
- **`CLAUDE.md`**: above all the architecture notes and the "things that will bite you" section,
  when a new trap was found or an old one was fixed. That section is the repo's real memory.
- **`SETUP.md`**: when setup, credentials, or the venv steps changed.
- **`leagues.json`**: when a season to league id mapping was discovered or corrected.
- **`.env.example`**: when a new setting was introduced, with a placeholder and never a value.
- **Per-user memory**: entries that name workflows, paths, or facts this session changed.

**Open actions** go in the README roadmap, never a new to-do file. Anything about league data or
a keeper decision belongs in `../personal/fantasy_football`, not here.

## Sensitivity check

- **This repo is public.** Nothing private may land in it: no league member names beyond what
  Yahoo already publishes, no other managers' artifacts, no screenshots of the draft board, and
  nothing from a work repo.
- **`.env` holds live credentials and OAuth tokens.** It stays gitignored, its values are never
  printed, and `doctor` verifies that on every run.
- **Archived league data** under `data/` is committed on purpose in the private league repo, so
  check before adding any data file here that it belongs in a public one.

## The gate

- **The tests pass.** They are plain scripts with asserts and there is no runner config, so run
  each directly with the venv interpreter: `tests\test_keepers.py` and `tests\test_pull.py`.
- **`doctor` is green offline:** `.\.venv\Scripts\python.exe -m ffball.doctor` reports a healthy
  setup and changes no state.
- **No live call was made** during the session, or it was deliberate and the user asked for it.
- **Writing register:** no em dashes, no en dashes, no semicolons in prose, in docs, comments,
  commit messages and any output text. Code syntax is unaffected, so statement semicolons stay.
- **Encoding:** no file was written through a PowerShell `Set-Content -Encoding utf8` or a
  `Get-Content -Raw` round-trip, both of which have caused real bugs here. Use .NET
  `WriteAllText` with a no-BOM UTF8 encoding instead.
- **The two commitments hold:** `pull` keeps its pacing and its resume behavior, and
  `Candidate.surplus_value` keeps its curve adjustment, which `tests/test_keepers.py` pins.
- **The two data shapes stay straight:** live model objects are read by attribute, `serialize()`
  produces the dicts that `pull` writes, and analysis reads the dict form only.

## The narrow gate

| Changed | Check |
|---|---|
| `ffball/keepers.py` or anything touching valuation | `tests\test_keepers.py` passes, the curve adjustment is intact |
| `ffball/pull.py`, `client.py`, or serialization | `tests\test_pull.py` passes, pacing and resume intact, dict form preserved |
| any other module | doctor offline is green, and the test whose area it touches |
| docs only (`README.md`, `SETUP.md`, `CLAUDE.md`) | writing register, nothing private in a public repo |
| `.env.example` or `leagues.json` | placeholders only, no BOM, valid JSON |
| memory files only | no check, state that |

## Commit rules

- **A terminal wrap never commits or pushes.**
- Commit by path, never `git add -A` and never `commit -a`. One commit per coherent chunk, and
  the test that proves a behavior travels with the change.
- Never a credential, a token, or a private league artifact, in a file or in a message.
- Message: a one-line summary, then grouped bullets saying what changed and how it was verified,
  ending with the trailer the session's operating instructions specify. Never hardcode a model
  name. The register applies to commit messages too.

## The pick-up prompt

1. One line: resuming yahoo_football on the branch actually checked out (read it, do not assume).
   Read `CLAUDE.md`, above all the architecture and "things that will bite you" sections, then
   `README.md` for the roadmap and `SETUP.md` if the environment is in question.
2. The standing rules, one line each: the repo is public so nothing private lands here, the venv
   interpreter is explicit, live API calls are not for checking things, the writing register, and
   the PowerShell UTF-8 traps.
3. "Verify the checkout first": `git status` clean, `git pull` current, `doctor` green offline,
   and both test scripts passing.
4. Three to six bullets of where we left off: the last commits' headlines, whether the Yahoo API
   access application has moved, what is implemented against the roadmap, and the agreed next
   steps. Manager identity across seasons is the precondition for the record book, so say where
   it stands.
5. "Then report status and wait for direction."
