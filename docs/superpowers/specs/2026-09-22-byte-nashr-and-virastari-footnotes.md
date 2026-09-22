# byte-nashr + virastari footnote markers — Design

## Purpose

Extend the Byte skills repo with a second skill, `byte-nashr`, that takes
a `byte-virastari`-corrected Persian text plus its loose images and turns
it into a properly formatted, schema-valid article in the
`byte-new-website` repo (content/issues/**), handling image optimization,
author resolution, and footnote→Tooltip conversion. This also extends the
existing `byte-virastari` skill with a footnote-marking capability.

## Part A — `byte-virastari` footnote markers

### Marker syntax

`byte-virastari` inserts, directly into the corrected text, inline
markers of the form:

```
{{واژه یا عبارت|توضیح کوتاه پاورقی}}
```

Double curly braces were chosen because they don't collide with Markdown/
MDX syntax already in use (headings, blockquotes, links, the `<Tooltip>`
component itself, KaTeX `$...$`). The marker is plain text — no JSX — so
the corrected file stays usable outside the website pipeline.

### When to mark

Candidates: technical jargon a general reader may not know, uncommon
abbreviations/acronyms, foreign proper nouns, terms whose meaning depends
on context the reader may lack. This is editorial judgment, not a fixed
list — documented as guidance (not a lookup table) in
`ghavaed-negareshi.md` or a new `references/footnote-ha.md`.

### Reporting

The change report (already required by the existing `byte-virastari`
SKILL.md workflow) gets a new section: every inserted marker, the term,
and the explanation — so a human editor can veto or edit a footnote
before publishing.

## Part B — `byte-nashr`

### Input

A single loose staging folder: the corrected text file (with `{{term|
explanation}}` markers) plus loose image files, no required subfolder
structure. `byte-nashr` is responsible for figuring out where everything
goes — this mirrors how editors currently work (copy text out of Google
Docs, drop images in a folder).

### Frontmatter authoring

`byte-nashr` writes the full frontmatter block itself:
`title`, `description`, `authors`, `tags`, `date`, `issue`, `order`,
`cover` — matching `articleFrontmatterSchema` in
`byte-new-website/lib/content/schema.ts`. It infers what it reasonably
can from the text and asks the user for anything it can't (issue number,
which issue's `order` slot, author identity — see below).

### Slug + placement

- Derives a Latin, kebab-case slug from the title (matching existing
  folder-naming convention under `content/issues/<issue>/<slug>/`).
- Creates `content/issues/<issue>/<slug>/index.mdx` and, if the article
  has images, `content/issues/<issue>/<slug>/img/`.
- Does not touch `content/issues/<issue>/meta.json` (issue-level
  metadata is out of scope — the skill only adds one article to an
  existing issue).

### Author resolution

- Matches the given author name(s) against `content/data/authors.ts` by
  name.
- **Always asks the user for confirmation** — whether an existing match
  was found or not — before deciding to reuse an id or create a new one
  (per your instruction: never decide silently).
- If a new author: asks for the missing profile fields the schema needs
  (`title`, `image`, `socials`), generates an `id` following the existing
  convention (seen in authors.ts: either an initialism like `AHMZ` or a
  camelCase full name like `aidaJabbari`), and appends a new entry to
  `AUTHORS` in `authors.ts`.
- Does **not** touch `staff.ts` unless the user explicitly says the
  person is editorial staff.

### Image handling

Applies to both article images and a new/updated author photo:

- **SVG**: copied through unchanged — the site already ships and serves
  SVGs directly (`images: { unoptimized: true }` in `next.config.ts`
  means no build-time processing happens anyway, confirmed by existing
  usage like `content/issues/00000110/xdp/img/img1.svg`).
- **Raster (PNG/JPG/WebP)**:
  - `< 150KB`: left as-is.
  - `150KB–300KB`: left as-is by default (acceptable range), but the
    skill may still convert to WebP if doing so is trivial and lossless
    enough — never required.
  - `> 300KB`: **must** be converted. Uses `cwebp` (confirmed present on
    this machine at `/opt/homebrew/bin/cwebp`) at a quality level chosen
    to land the output at or under 300KB while staying visually clean
    (start at quality 80, step down if still over budget). The `.mdx`
    image reference and the file on disk both end up `.webp`.
- Article images land in `content/issues/<issue>/<slug>/img/`; author
  photos land in `public/img/authors/` (existing convention).

### Footnote → Tooltip conversion

Every `{{term|explanation}}` marker in the corrected text becomes:

```jsx
<Tooltip tip="توضیح کوتاه"><span>واژه یا عبارت</span></Tooltip>
```

matching the existing `components/content/tooltip.tsx` usage pattern
seen in published articles.

### Validation

Before handing the result back, runs (via Bash, inside
`byte-new-website`):
- `pnpm typecheck`
- `pnpm test` (exercises `lib/content/schema.test.ts` and friends against
  the new content, since content is validated through the Zod schemas at
  build/read time)

Reports any failures to the user. **Does not** auto-commit, auto-push, or
run the full `pnpm build`/`pnpm prebuild` (which mutates `public/` and
regenerates OG images) — those remain manual/human steps.

## Out of scope

- Issue-level `meta.json` creation/editing.
- Adding new staff members to `staff.ts`.
- Auto-committing or pushing to the website repo.
- A generic image-optimization CLI/script outside the skill's own
  workflow — this is skill-instruction only, like `byte-virastari`.
