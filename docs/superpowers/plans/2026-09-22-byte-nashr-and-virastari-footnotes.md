# byte-nashr + virastari footnote markers Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add footnote-marking capability to `byte-virastari` and ship a new skill, `byte-nashr`, that converts a corrected text + loose images into a schema-valid article inside the `byte-new-website` repo.

**Architecture:** Content-authoring task (Markdown skill files), same as the first skill. `byte-nashr` lives alongside `byte-virastari` under `skills/`, following the same flat `skills/<name>/SKILL.md` layout. No app code changes to `byte-new-website` itself — only its content/data conventions are documented for the skill to follow.

**Tech Stack:** Plain Markdown + YAML frontmatter. `byte-nashr`'s image step shells out to `cwebp` (confirmed installed at `/opt/homebrew/bin/cwebp`). Validation step shells out to `pnpm typecheck` / `pnpm test` inside `byte-new-website`.

**Spec:** `docs/superpowers/specs/2026-09-22-byte-nashr-and-virastari-footnotes.md`

## Global Constraints

- Footnote marker syntax is exactly `{{واژه یا عبارت|توضیح کوتاه پاورقی}}` (double curly braces, pipe separator).
- `byte-nashr` always asks the user to confirm author identity — existing match or new — never decides silently.
- Image conversion: `<150KB` left as-is; `150–300KB` left as-is by default; `>300KB` must be converted via `cwebp`, targeting ≤300KB output, starting at quality 80 and stepping down if needed. SVGs always pass through unchanged.
- `byte-nashr` never auto-commits, auto-pushes, or runs `pnpm build`/`pnpm prebuild` in `byte-new-website`.
- Root `README.md` (English) must list every skill in the repo.

---

### Task 1: byte-virastari — footnote marker capability

**Files:**
- Modify: `skills/byte-virastari/SKILL.md`
- Create: `skills/byte-virastari/references/footnote-ha.md`

**Interfaces:**
- Consumes: nothing
- Produces: marker syntax `{{term|explanation}}` that Task 3 (byte-nashr's tooltip conversion) consumes as input format

- [ ] **Step 1: Write `references/footnote-ha.md`**

Content: the marker syntax spec (`{{واژه یا عبارت|توضیح کوتاه پاورقی}}`), guidance on what qualifies as footnote-worthy (technical jargon, uncommon abbreviations/acronyms, foreign proper nouns, context-dependent terms), 4-5 worked examples showing a sentence before/after marker insertion, and an explicit rule: don't over-mark — only terms a general Byte reader would plausibly not know.

- [ ] **Step 2: Update `skills/byte-virastari/SKILL.md`**

Add to the "مرجع‌ها" list: `references/footnote-ha.md`. Add a new step to the گردش‌کار (workflow) between style application and output generation: "شناسایی و علامت‌گذاری واژه‌های نیازمند پاورقی طبق `references/footnote-ha.md`، با درج نشانهٔ `{{واژه|توضیح}}` مستقیماً در متن". Add a new subsection to the change-report format: a "پاورقی‌های افزوده‌شده" section listing every marker inserted (term + explanation) so a human editor can veto any of them.

- [ ] **Step 3: Verify**

Run: `grep -c 'footnote-ha.md' skills/byte-virastari/SKILL.md` — expect ≥ 1.
Run: `test -f skills/byte-virastari/references/footnote-ha.md && echo OK`

- [ ] **Step 4: Commit**

```bash
git add skills/byte-virastari/SKILL.md skills/byte-virastari/references/footnote-ha.md
git commit -m "feat(byte-virastari): add footnote-marker workflow

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 2: byte-nashr — reference files

**Files:**
- Create: `skills/byte-nashr/references/schema-va-mahal.md` (frontmatter schema + file placement conventions)
- Create: `skills/byte-nashr/references/tasavir.md` (image handling rules)
- Create: `skills/byte-nashr/references/nevisandegan.md` (author resolution)

**Interfaces:**
- Consumes: conventions read from `byte-new-website/lib/content/schema.ts`, `byte-new-website/content/data/authors.ts`, `byte-new-website/content/data/staff.ts`, `byte-new-website/next.config.ts`, `byte-new-website/components/content/tooltip.tsx` (all already read this session)
- Produces: reference files that Task 3 (`SKILL.md`) links to by relative path

- [ ] **Step 1: Write `references/schema-va-mahal.md`**

Content: the `articleFrontmatterSchema` fields verbatim (`title`, `description`, `authors: string[]`, `tags: string[]`, `date: YYYY-MM-DD`, `issue: 8-digit binary string`, `order: non-negative int`, `cover?: string`), the folder placement convention (`content/issues/<issue>/<slug>/index.mdx` + `img/`), the slug convention (Latin kebab-case, matching existing folders like `distributed-systems`, `filesystem-without-limit-on-inode`), and an explicit note that issue-level `meta.json` is never created/edited by this skill — the issue folder must already exist.

- [ ] **Step 2: Write `references/tasavir.md`**

Content: the three-tier size policy (< 150KB as-is; 150–300KB as-is by default; > 300KB must convert), the exact `cwebp` invocation pattern (`cwebp -q 80 input.png -o output.webp`, re-run at lower quality — e.g. 70, 60 — if still over 300KB, never below quality 40 without flagging to the user), the SVG passthrough rule, and destination paths (article images → `content/issues/<issue>/<slug>/img/`, author photos → `public/img/authors/`).

- [ ] **Step 3: Write `references/nevisandegan.md`**

Content: the `AuthorRecord` fields (`id`, `name`, `title?`, `image?`, `bio?`, `socials`), the two observed `id` conventions (initialism like `AHMZ`, or camelCase full name like `aidaJabbari`) with guidance to follow whichever style is more common for similar names, the explicit rule to **always ask the user to confirm** — whether reusing an existing id or creating a new one — before writing anything, and the rule that `staff.ts` is only touched when the user explicitly says the person is editorial staff.

- [ ] **Step 4: Verify**

Run: `test -f skills/byte-nashr/references/schema-va-mahal.md && test -f skills/byte-nashr/references/tasavir.md && test -f skills/byte-nashr/references/nevisandegan.md && echo OK`

- [ ] **Step 5: Commit**

```bash
git add skills/byte-nashr/references/
git commit -m "feat(byte-nashr): add schema, image, and author reference files

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 3: byte-nashr — SKILL.md

**Files:**
- Create: `skills/byte-nashr/SKILL.md`

**Interfaces:**
- Consumes: `references/schema-va-mahal.md`, `references/tasavir.md`, `references/nevisandegan.md` from Task 2 (relative links); the `{{term|explanation}}` marker format from Task 1
- Produces: the installable skill entry point

- [ ] **Step 1: Write frontmatter**

```yaml
---
name: byte-nashr
description: Publishes a byte-virastari-corrected Persian article (text + loose images) into the byte-new-website repo as a schema-valid MDX article — writes frontmatter, resolves/creates authors, optimizes images (SVG passthrough, WebP conversion over 300KB), converts {{term|explanation}} footnote markers into Tooltip components, and validates against the site's content schema. Use when asked to add/publish an edited Byte article to the website.
---
```

- [ ] **Step 2: Write the Persian body**

Sections, each referencing the appropriate file from Task 2:
- توضیح کوتاه و «کِی فعال شو» (وقتی کاربر متن ویراستاری‌شده‌ای دارد و می‌خواهد آن را به `byte-new-website` اضافه کند)
- گردش‌کار هفت‌مرحله‌ای مطابق spec: (۱) خواندن پوشهٔ ورودی (متن + عکس‌های آزاد)، (۲) نگارش frontmatter طبق `references/schema-va-mahal.md`، (۳) تعیین اسلاگ و مسیر مقصد، (۴) تحلیل و تأیید نویسنده(ها) طبق `references/nevisandegan.md` (همیشه از کاربر تأیید بگیر)، (۵) پردازش تصاویر طبق `references/tasavir.md`، (۶) تبدیل نشانه‌های `{{واژه|توضیح}}` به `<Tooltip tip="توضیح"><span>واژه</span></Tooltip>`، (۷) اعتبارسنجی با اجرای `pnpm typecheck` و `pnpm test` داخل ریپوی `byte-new-website`
- تأکید صریح: هرگز commit/push نکن، هرگز `pnpm build`/`pnpm prebuild` اجرا نکن (چون `public/` را تغییر می‌دهد و OG images را بازتولید می‌کند)
- اگر مسیر ریپوی `byte-new-website` مشخص نیست، از کاربر بپرس (پیش‌فرض این جلسه: `/Users/moeein/Documents/byte/byte-new-website`، اما مسیر می‌تواند تغییر کند)

- [ ] **Step 3: Verify links resolve**

Run: `grep -o 'references/[a-z-]*\.md' skills/byte-nashr/SKILL.md` — expect all three of `references/schema-va-mahal.md`, `references/tasavir.md`, `references/nevisandegan.md`.
Run: `test -f skills/byte-nashr/references/schema-va-mahal.md && test -f skills/byte-nashr/references/tasavir.md && test -f skills/byte-nashr/references/nevisandegan.md && echo OK`

- [ ] **Step 4: Verify frontmatter parses**

Run: `head -5 skills/byte-nashr/SKILL.md` — confirm valid YAML delimiters and `name:`/`description:` present.

- [ ] **Step 5: Commit**

```bash
git add skills/byte-nashr/SKILL.md
git commit -m "feat(byte-nashr): add SKILL.md entry point

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 4: Update root README and verify

**Files:**
- Modify: `README.md`

**Interfaces:**
- Consumes: everything from Tasks 1-3

- [ ] **Step 1: Add `byte-nashr` row to the skills table**

In the "Available skills" table, add a row:
`| [\`byte-nashr\`](skills/byte-nashr/SKILL.md) | Publishes a byte-virastari-corrected article into the byte-new-website repo — frontmatter, authors, image optimization, footnote→Tooltip conversion. |`

- [ ] **Step 2: Full repo tree verification**

Run: `find . -not -path './.git*' -not -path './docs*' -type f | sort`
Expected to include (in addition to Task-1-era files):
```
./skills/byte-nashr/SKILL.md
./skills/byte-nashr/references/nevisandegan.md
./skills/byte-nashr/references/schema-va-mahal.md
./skills/byte-nashr/references/tasavir.md
./skills/byte-virastari/references/footnote-ha.md
```

- [ ] **Step 3: Commit and push**

```bash
git add README.md
git commit -m "docs: list byte-nashr in README

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
git push
```

Confirm: `git status` clean, `git log --oneline -8` shows all new commits.
