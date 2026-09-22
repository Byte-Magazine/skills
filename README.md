# Byte Magazine — Agent Skills

Agent Skills for [Byte Magazine](https://byte-mag.ir) (نشریهٔ بایت) — reusable
instructions for AI coding/editorial agents that encode how Byte does
things: editorial standards, house style, and other recurring workflows.

Skills in this repo follow the open
[Agent Skills](https://github.com/vercel-labs/skills) convention
(`skills/<name>/SKILL.md`), so they install with a single command into
Claude Code, Cursor, and 40+ other agents — no npm package, no manual
copy-pasting.

## Install

Using [`npx skills`](https://github.com/vercel-labs/skills):

```bash
# List every skill in this repo
npx skills add Byte-Magazine/skills --list

# Install all skills
npx skills add Byte-Magazine/skills

# Install one specific skill
npx skills add Byte-Magazine/skills --skill byte-virastari

# Install into a specific agent (default: auto-detects installed agents)
npx skills add Byte-Magazine/skills -a claude-code
```

By default, skills install into your project's `.claude/skills/`
(project scope). Add `-g` to install globally into `~/.claude/skills/`
instead, so the skill is available in every project:

```bash
npx skills add Byte-Magazine/skills -g
```

## Using skills in Claude Code

Once installed, Claude Code discovers the skill automatically — you don't
need to do anything else. Just ask for the task the skill covers (e.g.
"از این اسکیل بایت رو ویراستاری کن") and Claude invokes it on its own.
You can also invoke a skill explicitly by name if your agent supports
slash commands (e.g. `/byte-virastari`).

## Using skills in other agents

The `npx skills` CLI supports 40+ agents (Cursor, Windsurf, Codex, Cline,
and more) — pass `-a <agent-name>` to target one, or omit it to install
into every agent it detects on your machine. See the
[vercel-labs/skills README](https://github.com/vercel-labs/skills) for
the full list of supported agents and flags.

## Available skills

| Skill | Description |
| --- | --- |
| [`byte-virastari`](skills/byte-virastari/SKILL.md) | Persian copy-editing (ویراستاری نگارشی) for Byte Magazine articles — applies Farhangestan-based Persian orthography rules and Byte's house style. |
| [`byte-nashr`](skills/byte-nashr/SKILL.md) | Publishes a byte-virastari-corrected article into the byte-new-website repo — writes frontmatter, resolves/creates authors, optimizes images, and converts footnote markers into Tooltip components. |

## Adding a new skill

Create a new folder under `skills/<skill-name>/` with a `SKILL.md` file
(YAML frontmatter with `name` and `description`, followed by the skill's
instructions). Reference material the skill needs can live alongside it
in `skills/<skill-name>/references/`. No manifest file or build step is
required — the `npx skills` CLI discovers any `skills/<name>/SKILL.md`
in the repo automatically.
