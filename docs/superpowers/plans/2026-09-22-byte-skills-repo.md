# Byte Skills Repo Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Stand up the Byte Magazine skills repo infrastructure (installable via `npx skills add Byte-Magazine/skills`) and ship the first skill, `byte-virastari`, a Persian copy-editing skill built from the Farhangestan orthography rulebook and Byte's house style.

**Architecture:** Flat `skills/<name>/SKILL.md` layout (vercel-labs/skills convention, no manifest needed). Root `README.md` is English; everything under `skills/byte-virastari/` is Persian. This is a content-authoring project (markdown reference files, not application code), so "tests" are verification checks (file exists, required sections present, examples match source material) rather than unit tests.

**Tech Stack:** Plain Markdown + YAML frontmatter. No build step, no npm package.

**Spec:** `docs/superpowers/specs/2026-09-22-byte-skills-repo-design.md`

## Global Constraints

- Repo root `README.md` must be in English (per spec).
- Everything under `skills/byte-virastari/` must be in Persian (per spec).
- Skill layout must be `skills/<skill-name>/SKILL.md` (flat, root-level `skills/` dir) — required for `npx skills add` auto-discovery.
- No npm package.json / publish step — repo-compatibility only.
- `byte-virastari` output is always two files (corrected text + change report), never chat-only output.
- Ambiguous cases (e.g. کسرهٔ اضافه) must be flagged in the change report, not silently resolved.

---

### Task 1: Repo infra — README and .gitignore

**Files:**
- Create: `README.md`
- Create: `.gitignore`

**Interfaces:**
- Consumes: nothing
- Produces: nothing consumed by later tasks (informational only)

- [ ] **Step 1: Write `.gitignore`**

Standard OS/editor noise (`.DS_Store`, `node_modules/`, `*.log`).

- [ ] **Step 2: Write `README.md`**

English. Sections: what this repo is (Byte Magazine's agent skills), install (`npx skills add Byte-Magazine/skills` — list all, or `--skill byte-virastari` for one), usage in Claude Code (project vs global scope: `.claude/skills/` vs `~/.claude/skills/`) and other agents (mention the `-a` flag / vercel-labs/skills supports 40+ agents), list of available skills with one-line descriptions (currently: `byte-virastari`), contribution note (new skills follow `skills/<name>/SKILL.md`).

- [ ] **Step 3: Verify**

Run: `ls -la` — confirm `README.md` and `.gitignore` exist at repo root.
Run: `git status` — confirm both are untracked (not ignored by their own `.gitignore`).

- [ ] **Step 4: Commit**

```bash
git add README.md .gitignore
git commit -m "chore: add repo README and gitignore

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 2: byte-virastari — ghavaed-negareshi.md (rules reference)

**Files:**
- Create: `skills/byte-virastari/references/ghavaed-negareshi.md`

**Interfaces:**
- Consumes: source material already read into context this session — `/Users/moeein/Downloads/نکات ویراستاری نگارشی/index.html` + `images/image1.png`..`image8.png`, and the official rulebook PDF pages 21–43 (`/Users/moeein/Downloads/نکات ویراستاری نگارشی/reference/dastur-khat-farsi-1394.pdf`)
- Produces: a rules reference that Task 4 (SKILL.md) links to by relative path `references/ghavaed-negareshi.md`

- [ ] **Step 1: Write the reference file**

Structure as one `##` section per topic, each with the rule statement and ✗ wrong → ✓ correct examples pulled from the source material already extracted this session:
1. اصول کلی (۷ اصل از صفحهٔ ۹-۱۰ کتاب: حفظ چهره خط، استقلال از عربی، تطابق مکتوب/ملفوظ، فراگیر بودن قاعده، سهولت نوشتن/خواندن، سهولت آموزش، فاصله‌گذاری برای استقلال کلمه)
2. جدول فاصله‌گذاری واژه‌های پرکاربرد (ای، این/آن، همین، هیچ، چه، را، که، بی، می‌/همی، هم، تر/ترین، ها) — از صفحات ۲۱-۲۳ کتاب
3. ضمایر ملکی و مفعولی (ـم، ـت، ـش، ـمان، ـتان، ـشان) — جدول صفحهٔ ۲۶
4. یای نکره، مصدری و نسبی — جدول صفحهٔ ۲۷
5. کسرهٔ اضافه (نوشته نمی‌شود مگر برای رفع ابهام) — صفحهٔ ۲۸
6. همزهٔ میانی و پایانی (قواعد کرسی: ا/آ، و، ی، بدون کرسی) — صفحات ۲۹-۳۱ + جدول راهنمای کتابت همزه
7. واژه‌های مأخوذ از عربی (تای گرد، الف کوتاه، تنوین، تشدید) — صفحات ۳۵-۳۸
8. ترکیبات — کلماتی که الزاماً پیوسته نوشته می‌شوند (۱۵ قاعده، صفحات ۳۹-۴۳) و کلماتی که الزاماً جدا نوشته می‌شوند (۸ قاعده، صفحات ۴۱-۴۳)
9. جدول‌های تکمیلی از سند داخلی بایت (index.html + images):
   - نکات مهم (استفاده از ـه به‌جای ـهی، تنوین‌ها، کلمات انگلیسی)
   - املای نادرست/درست پرکاربرد (آنقدر→آنقدر نه, شستشو→شست‌وشو, گفتگو→گفت‌وگو, علاقمند→علاقه‌مند, بیاندیش→بیندیش, و بقیهٔ جدول)
   - واژه‌های «این»دار که نیم‌فاصله می‌گیرند (جدول کامل از image6)
   - حروف ربطی که با فاصله نوشته می‌شوند (جدول کامل از image8: از آنجا که، از زمانی که، ...)
   - نکتهٔ «به این‌که / به آن‌که / درحالی‌که» با نیم‌فاصله
   - «اینجا/اینجانب» بی‌فاصله (استثنا)
   - قاعدهٔ «بن مضارع + و + بن مضارع» و «مند» با «ه»
   - بیندیش/بیفت/بینداز با دو نقطه، نه بیاندیش/بیافت/بیانداز

- [ ] **Step 2: Verify content fidelity**

Re-check each ✗→✓ example pair against the images/PDF pages already viewed this session (do not invent examples not present in the source material).

- [ ] **Step 3: Verify file structure**

Run: `grep -c '^## ' skills/byte-virastari/references/ghavaed-negareshi.md` — expect at least 9 (one per topic above).

- [ ] **Step 4: Commit**

```bash
git add skills/byte-virastari/references/ghavaed-negareshi.md
git commit -m "feat(byte-virastari): add orthography rules reference

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 3: byte-virastari — sabk-e-byte.md (house style)

**Files:**
- Create: `skills/byte-virastari/references/sabk-e-byte.md`

**Interfaces:**
- Consumes: `/Users/moeein/Documents/byte/byte-new-website/content/issues/00000101/distributed-systems/index.mdx` (already read this session) as the primary style sample; may sample 1-2 more `.mdx` files from `content/issues/**` or `content/blog/**` if useful
- Produces: a style reference that Task 4 (SKILL.md) links to by relative path `references/sabk-e-byte.md`

- [ ] **Step 1: Sample one more article for style confirmation**

Read one additional file, e.g. `/Users/moeein/Documents/byte/byte-new-website/content/blog/bugsbuzzy/index.mdx`, to confirm the style notes below aren't a one-article fluke.

- [ ] **Step 2: Write the reference file**

Document, with examples pulled from the sampled articles:
- نیم‌فاصله consistently used (می‌دهد، شده‌است/شده است — note observed variation, recommend standard `شده است` per rulebook §۲۲ unless house convention says otherwise)
- اصطلاحات فنی انگلیسی به خط لاتین نگه داشته می‌شوند (نه ترجمه و نه فارسی‌نویسی) — مثال: CAP Theorem, MongoDB, Cassandra
- ساختار heading: `##` برای بخش‌های اصلی، لحن سرخط‌ها کوتاه و گیرا
- نقل‌قول‌های تأکیدی با `>` (blockquote) برای معرفی منابع/جمع‌بندی
- لحن: مستقیم، پرانرژی، مخاطب را با «شما»/سؤال خطاب می‌کند، مثال‌های ملموس (مهمانی یک میلیون نفره) به‌جای توضیح انتزاعی صرف
- اعداد فارسی (۱۵، ۹۵) در متن فارسی، نه لاتین
- استفاده از em dash فارسی (‑) برای شبه‌جمله‌های توضیحی داخل جمله

- [ ] **Step 3: Verify**

Run: `test -f skills/byte-virastari/references/sabk-e-byte.md && echo OK`

- [ ] **Step 4: Commit**

```bash
git add skills/byte-virastari/references/sabk-e-byte.md
git commit -m "feat(byte-virastari): add Byte house-style reference

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 4: byte-virastari — SKILL.md

**Files:**
- Create: `skills/byte-virastari/SKILL.md`

**Interfaces:**
- Consumes: `references/ghavaed-negareshi.md` and `references/sabk-e-byte.md` from Tasks 2-3 (relative links)
- Produces: the installable skill entry point

- [ ] **Step 1: Write frontmatter**

```yaml
---
name: byte-virastari
description: Persian copy-editing (ویراستاری نگارشی) for Byte Magazine articles — applies Farhangestan-based Persian orthography rules and Byte's house style. Use when asked to edit, proofread, or correct a Persian text for Byte, or to check نیم‌فاصله/spacing/spelling per Byte's editorial standard.
---
```

- [ ] **Step 2: Write the Persian body**

Include:
- توضیح کوتاه دربارهٔ اینکه این اسکیل چه‌کاری انجام می‌دهد و چه زمانی باید فعال شود
- گردش‌کار پنج‌مرحله‌ای مطابق spec (خواندن متن کامل → اعمال قواعد `references/ghavaed-negareshi.md` → اعمال سبک `references/sabk-e-byte.md` → تولید دو فایل خروجی → پرچم‌گذاری موارد مبهم)
- فرمت دقیق دو فایل خروجی: نام‌گذاری (مثلاً `<original-name>-virastari-shode.<ext>` و `<original-name>-gozaresh-taghirat.md`)، ساختار گزارش تغییرات (دسته‌بندی بر اساس نوع قاعده، هر مورد: قبل → بعد + دلیل + ارجاع به بخش مربوطه در ghavaed-negareshi.md)
- تأکید صریح: هرگز به‌صورت خاموش/بی‌گزارش متن را بازنویسی نکند؛ برای موارد مبهم (مثل کسرهٔ اضافه) حدس نزند و در گزارش پرچم بزند
- ارجاع به دو فایل reference با مسیر نسبی

- [ ] **Step 3: Verify links resolve**

Run: `grep -o 'references/[a-z-]*\.md' skills/byte-virastari/SKILL.md` — expect both `references/ghavaed-negareshi.md` and `references/sabk-e-byte.md`, and confirm both files exist:
`test -f skills/byte-virastari/references/ghavaed-negareshi.md && test -f skills/byte-virastari/references/sabk-e-byte.md && echo OK`

- [ ] **Step 4: Verify frontmatter parses**

Run: `head -5 skills/byte-virastari/SKILL.md` — confirm valid YAML delimiters (`---` ... `---`) and `name:`/`description:` present.

- [ ] **Step 5: Commit**

```bash
git add skills/byte-virastari/SKILL.md
git commit -m "feat(byte-virastari): add SKILL.md entry point

Co-Authored-By: Claude Sonnet 5 <noreply@anthropic.com>"
```

---

### Task 5: End-to-end verification

**Files:** none created — verification only

**Interfaces:**
- Consumes: everything from Tasks 1-4

- [ ] **Step 1: Verify full repo tree matches spec layout**

Run: `find . -not -path './.git*' -not -path './docs*' -type f | sort`
Expected output includes exactly:
```
./.gitignore
./README.md
./skills/byte-virastari/SKILL.md
./skills/byte-virastari/references/ghavaed-negareshi.md
./skills/byte-virastari/references/sabk-e-byte.md
```

- [ ] **Step 2: Dry-run discovery with the skills CLI**

Run: `npx skills@latest add . --list` (from repo root, pointing at the local checkout if the CLI supports a local path; otherwise skip and note that discovery was verified structurally in Step 1 since the repo isn't pushed yet).

- [ ] **Step 3: Push and confirm remote**

```bash
git push -u origin main
```

Confirm: `git log --oneline -5` shows all 4 feature commits, and `git status` is clean.
