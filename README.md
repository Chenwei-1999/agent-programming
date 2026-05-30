<h1 align="center">Programmer</h1>

<p align="center">
  <b>Senior-engineer judgment from eight books, distilled into one portable skill.</b>
</p>

<p align="center">
  <a href="https://developers.openai.com/codex/skills"><img src="https://img.shields.io/badge/Codex-Skill-111827?style=for-the-badge&logo=openai&logoColor=white" alt="Codex Skill"></a>
  <img src="https://img.shields.io/badge/Claude-Code-6b46c1?style=for-the-badge" alt="Claude Code compatible">
  <img src="https://img.shields.io/badge/Gemini-CLI-4285f4?style=for-the-badge" alt="Gemini CLI compatible">
  <img src="https://img.shields.io/badge/Books-8-0f766e?style=for-the-badge" alt="Eight books">
  <img src="https://img.shields.io/badge/Design-Two--Tier-f59e0b?style=for-the-badge" alt="Two-tier design">
</p>

Programmer is a portable agent skill that gives a coding agent senior-engineer
judgment across naming, functions, comments, error handling, complexity,
module and OO design, SOLID, architecture boundaries, data systems,
concurrency, testing, code review, long-term engineering trade-offs, and
idiomatic Python. It is designed for SKILL.md-compatible agents, including
Codex, Claude Code, Gemini CLI, and generic agents that support
filesystem-installed skills.

Eight canonical software-engineering books are distilled into one skill. They
share a single claim, seen from different angles:

> **Software is read and changed far more than it is written. The job is to
> manage complexity so the system stays understandable and changeable over its
> whole life — and to make sound trade-offs when "best practice" runs out.**

Every rule is tagged with the book(s) that back it. Where books independently
agree, that convergence is the signal to trust; where they disagree, the skill
takes a reconciled stance.

## Quick Start

Give this GitHub link to your coding agent:

```text
Install this skill: https://github.com/Chenwei-1999/agent-programming
```

That's the intended install path: the repo is a self-contained skill directory,
so a local agent can clone it into its skills folder and start using it.

If your agent supports `skill-installer`, the equivalent single command is:

```text
$skill-installer install https://github.com/Chenwei-1999/agent-programming
```

## Why It Exists

Most "be a better engineer" advice for agents collapses into vibes — *write
clean code*, *be careful*. This skill keeps it grounded:

- Distills eight books into rules that are **tagged by source**, so guidance is
  traceable rather than generic.
- Resolves the places the books **disagree** (e.g. many-small-classes/SOLID vs.
  deep modules) into a single reconciled stance instead of contradictory advice.
- Calibrates effort to `expected lifespan × number of future readers` — full
  rigor for long-lived shared code, a deliberately lighter touch for spikes and
  throwaways.
- Keeps a **two-tier** structure: cheap embedded judgment for ordinary work, and
  a deliberate path to primary sources for high-stakes or unfamiliar calls.
- **Fetches the source on demand:** when a call is high-stakes, the agent finds
  and reads the actual book — not just the distilled rules — and no PDFs ship
  with the repo, so there is nothing to download or set up first.
- Stays model-neutral: it names *roles* and maps them to whatever subagents your
  runtime provides.

## The Eight Books

| Book | Angle of attack | Catalog |
|------|-----------------|---------|
| *A Philosophy of Software Design* — Ousterhout | Structure / complexity, deep modules | `references/aposd-complexity.md` |
| *Clean Code* — Martin | Construction: naming, functions, smells | `references/clean-code.md` |
| *Code Complete*, 2e — McConnell | Construction at scale, hiding secrets | `references/code-complete.md` |
| *Clean Architecture* — Martin | SOLID, boundaries, dependency direction | `references/clean-architecture.md` |
| *Designing Data-Intensive Applications* — Kleppmann | Systems at scale, storage, replication | `references/ddia.md` |
| *Software Engineering at Google* — Winters et al. | Engineering over time and teams | `references/swe-at-google-principles.md` |
| *The Pragmatic Programmer* — Hunt & Thomas | Craft and practice | `references/pragmatic-design.md`, `references/pragmatic-craft.md`, `references/pragmatic-tips-catalog.md` |
| *Fluent Python*, 2e — Ramalho | Idiomatic Python | `references/fluent-python.md` |

## How It Works

```text
your problem
  design · review · refactor · debug · explain · trade-off
        |
        v
Tier 1 — embedded judgment (always available)
  SKILL.md rules, each tagged with the book(s) behind it
  references/<book>.md per-book catalogs
        |
        |  high stakes? unfamiliar? books conflict? citing a source?
        v
Tier 2 — self-directed deep reference
  read references/<book>.md chapter map for the exact heading
  agent fetches the right edition into assets/<slug>.pdf on demand
  scripts/read_book.py searches it; or delegate the read to a scout subagent
        |
        v
you integrate the evidence and make the call
```

Tier 1 handles ordinary work without leaving the skill. Tier 2 is a deliberate
decision — taken only when the stakes or unfamiliarity justify reading source —
and the main agent always owns the final judgment.

## Agent Adapters

The skill does not require one specific model. It names roles and maps them to
whatever your coding agent supports.

| Role | Job | Codex adapter | Claude Code adapter | Gemini CLI adapter | Generic coding agents |
|------|-----|---------------|---------------------|--------------------|-----------------------|
| `docs-scout` | Read a `references/<book>.md` section or a book PDF and report the answer | `docs_researcher` / mini model | `docs-researcher` (Haiku-class) | Gemini docs/search session | cheap read-only docs worker |
| `code-scout` | Find where the answer lives in the codebase | `code_scout` / fast model | `code-scout` (Sonnet-class) | read-only Gemini session | fast read-only code scanner |
| `main-synthesizer` | Weigh the evidence and make the call | current main agent | Opus/Sonnet-class main agent | primary Gemini CLI session | strongest available agent |

If a runtime has no subagents, do the same lookups inline. The quality goal is
sound judgment backed by evidence; delegation is only a cost optimization.

## Tier-2 Source Lookup

The per-book catalogs in `references/` are the always-available distilled
source and are sufficient for most work. **Raw book PDFs are not shipped with
this skill** — no copies, no download links bundled in the repo.

Instead, when a problem needs primary-source text (a high-stakes choice, a
contested point, or a quote to cite), the skill has the agent **find and fetch
the book for you**: it uses the runtime's web-search/fetch tools to locate an
available copy of the specific **edition** pinned in `SKILL.md` →
*Additional Resources*, saves it to `assets/<slug>.pdf`, and reads it — no
manual setup. (You stay in control of what gets fetched and are responsible for
using copies you are entitled to in your jurisdiction.) Then:

```bash
python3 scripts/read_book.py --list
python3 scripts/read_book.py clean-code --query "meaningful names" --context 8
```

The reader extracts text via `pdftotext`, `pypdf`, `PyPDF2`, or a built-in
fallback. **Edition matters** — *Fluent Python*, *The Pragmatic Programmer*, and
*DDIA* renumber chapters between editions, so the catalogs pin an edition. Always
cite by **heading text, not page or number**, which stays stable across copies
of the same edition. See `assets/README.md` for details.

## Manual Install

Clone the repo into your agent's skills directory under the name `programmer`:

```bash
git clone https://github.com/Chenwei-1999/agent-programming.git ~/.claude/skills/programmer
```

Common skill locations:

- Codex: `~/.codex/skills/programmer`
- Claude Code: `~/.claude/skills/programmer`
- Gemini CLI: `~/.gemini/skills/programmer`
- Generic local agents: `~/.agents/skills/programmer`

For Cursor, Continue, Aider, OpenHands, or another runtime with its own
convention, clone into that runtime's skill directory instead.

## Package Layout

```text
programmer/
  README.md
  SKILL.md                       # router + judgment, rules tagged by book
  references/
    aposd-complexity.md
    clean-code.md
    code-complete.md
    clean-architecture.md
    ddia.md
    fluent-python.md
    swe-at-google-principles.md
    pragmatic-design.md
    pragmatic-craft.md
    pragmatic-tips-catalog.md
  scripts/
    read_book.py                 # list / read / search a book PDF
  assets/
    README.md                    # how to supply book PDFs (not committed)
```

## Design Notes

The skill separates cheap, embedded judgment (Tier 1) from deliberate
primary-source lookup (Tier 2), and high-volume scanning (scout subagents) from
high-stakes synthesis (the main agent). That keeps cost low without lowering
quality: ordinary decisions never leave the distilled rules, and the expensive
path is reserved for when it actually changes the answer.

Book PDFs are deliberately kept out of the repository. The catalogs under
`references/` are original distillations; the source texts remain the property
of their authors and publishers, and you supply your own legitimately-obtained
copies when you need Tier 2.
