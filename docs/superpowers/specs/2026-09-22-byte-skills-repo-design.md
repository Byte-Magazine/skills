# Byte Magazine Skills Repo — Design

## Purpose

A public-ish skills repository for Byte Magazine (نشریهٔ بایت), installable
by anyone via `npx skills add Byte-Magazine/skills` (the vercel-labs/skills
CLI, compatible with Claude Code, Cursor, and 40+ other agents). It will
hold multiple reusable skills for the magazine's editorial/technical
workflows. The first skill is a Persian copy-editing assistant
(`byte-virastari`).

## Repo infrastructure

The `npx skills add` CLI auto-discovers skills in a flat layout:
`skills/<skill-name>/SKILL.md`. No manifest file is required.

```
/                        (repo root — remote already set: github.com/Byte-Magazine/skills)
├── README.md            # English. What this repo is, install instructions,
│                         # how to use skills in Claude Code / other agents,
│                         # list of available skills.
├── .gitignore
└── skills/
    └── byte-virastari/
        ├── SKILL.md
        └── references/
            ├── ghavaed-negareshi.md
            └── sabk-e-byte.md
```

- Root `README.md`: English, per user instruction.
- Everything under `skills/byte-virastari/`: Persian, per user instruction.
- Future skills follow the same `skills/<name>/SKILL.md` pattern.

## Skill: byte-virastari

### Frontmatter (SKILL.md)

```yaml
---
name: byte-virastari
description: Persian copy-editing (ویراستاری نگارشی) for Byte Magazine
  articles — applies the Farhangestan-based orthography rules and Byte's
  house style. Use when asked to edit/proofread/correct a Persian text
  for Byte.
---
```//description text is English for cross-agent discoverability; skill body is Persian.

### Behavior / workflow

1. Read the full input text (from a file path or pasted text).
2. Apply the rule set in `references/ghavaed-negareshi.md` systematically:
   spacing (چسبیده / جدا / نیم‌فاصله) for این، آن، همین، هیچ، چه، را، که،
   بی، می‌، هم، تر/ترین، ها؛ ضمایر ملکی و مفعولی؛ یای نکره و مصدری و نسبی؛
   کسرهٔ اضافه؛ همزهٔ میانی و پایانی؛ ترکیبات پیوسته/جدا؛ common
   incorrect→correct spelling pairs.
3. Apply Byte's house style from `references/sabk-e-byte.md` (نیم‌فاصله
   discipline, English technical terms kept in Latin script, heading/
   blockquote conventions, tone).
4. Produce two output files (not just chat text):
   - the corrected text (same format as input, e.g. `.mdx`/`.md`/`.txt`)
   - a change report listing each fix with a short reason, grouped by rule
     category, so a human editor can review quickly
5. Flag genuinely ambiguous cases (e.g. کسرهٔ اضافه disambiguation) in the
   report instead of silently guessing.

### references/ghavaed-negareshi.md

Distilled, example-driven rules extracted from:
- `/Users/moeein/Downloads/نکات ویراستاری نگارشی/index.html` + its 8 images
  (quick-reference notes and correct/incorrect word pairs)
- The official Farhangestan "دستور خط فارسی" (16th ed., 1394), downloaded
  from apll.ir and read in full (56 pages) — general principles, letter
  joining rules (lightly summarized, low editorial value for an LLM),
  spacing/attachment rules for prefixes/suffixes, pronoun suffixes, یای
  نکره/مصدری/نسبی, کسرهٔ اضافه, همزه, تنوین/تشدید, compound-word
  joining/separation rules (15 "always joined" + 8 "always separate"
  cases), and the multi-spelling word reference lists.

Organized as a rules reference (not prose), so the skill can look things
up efficiently: one section per topic, each with the rule statement and
a few examples (✗ wrong → ✓ correct).

### references/sabk-e-byte.md

House style notes derived from reading published Byte issues in
`/Users/moeein/Documents/byte/byte-new-website/content/issues/**`:
consistent نیم‌فاصله use, English technical terms/names kept in Latin
script inline, heading (`##`) and pull-quote (`>`) conventions, direct
and energetic tone, informal-but-precise register.

## Out of scope

- No npm package / publish step (confirmed: repo-compatibility only).
- No automated linter/CLI tool — this is an LLM-instruction skill, not a
  script.
- Full letter-glyph-joining tables from the rulebook (pages ~11–19) are
  not reproduced in detail — an LLM doesn't need Arabic-script glyph
  joining forms explained; only the substantive editorial rules matter.
