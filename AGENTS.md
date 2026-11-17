# AGENTS.md — smanjit.com

Context for AI coding agents (Claude Code, Copilot, Cursor, Codex, etc.) working in this repo.
Read this file fully before making any change. When a rule here conflicts with what you see
in an existing file, **this file wins** — the existing file is probably one of the known
inconsistencies listed at the bottom.

> ⚠️ **This is a PUBLIC repository.** Everything committed here is visible to anyone.
> See [Public repo rules](#public-repo-rules) before adding any content.

---

## 1. What this is

Personal portfolio + technical blog for Manjit Sithuraj, served at **https://smanjit.com**.

- **Hosting:** GitHub Pages from the `main` branch, custom domain via `CNAME`.
- **Stack:** hand-written static HTML + inline CSS + small inline vanilla JS.
- **No build step, no framework, no package manager, no dependencies.** Do not add any.
- **Deploy:** push to `main` → GitHub Pages publishes automatically.

## 2. File map

```
/
├── index.html                 # Single-page portfolio (hero, about, experience, projects, skills, contact)
├── favicon.svg                # "M" monogram, accent on dark
├── profile.jpg                # Hero photo (rendered grayscale, colour on hover)
├── Manjit_Sithuraj_Resume_Print.pdf   # Linked from the "Download Resume" button
├── CNAME                      # smanjit.com — never edit or delete
├── _config.yml                # Jekyll config: excludes AGENTS.md / CLAUDE.md from the Pages build
├── AGENTS.md / CLAUDE.md      # Agent context (this file)
└── blog/
    ├── index.html             # Blog listing with tag filter
    ├── _post-template.html    # Canonical post template (not published — underscore prefix)
    ├── <slug>.html            # One self-contained file per article
    └── images/                # Post images: <slug>-cover.png, <slug>-<name>.png
```

Every page is **self-contained**: its CSS lives in a `<style>` block in its own `<head>`.
There is no shared stylesheet. When you change a design token, update it in every file.

## 3. Design system

### Colour tokens (identical `:root` block in every page)

| Token | Value | Use |
|---|---|---|
| `--bg` | `#0D0D0D` | Page background |
| `--surface` | `#1A1A1A` | Cards, code blocks, callouts |
| `--surface2` | `#222` | Inline code, avatar, nested surfaces |
| `--accent` | `#DA7756` | Primary accent: links, labels, numbers, highlights |
| `--blue` | `#4A7FA5` | Secondary accent: periods, tech stacks, secondary tags |
| `--text` | `#E8E0D8` | Headings and strong text |
| `--muted` | `#888880` | Body/secondary text |
| `--border` | `#2A2A2A` | All borders and dividers |

Accent tints are written as `rgba(218,119,86,<alpha>)` (`.04`–`.5`); blue tints as `rgba(74,127,165,<alpha>)`.
Only exceptions: code highlight colours inside `.code-block`, and red/green (`#e05c5c` / `#5cb85c`) for "bad/good" comparison cards.
**Do not introduce new colours.**

### Typography

- **Inter** (300–800) for all text; **JetBrains Mono** (400/500) for labels, meta lines, code, numbers, the logo.
- Both loaded from the same Google Fonts URL in every `<head>` — copy it exactly.
- Labels and meta lines: JetBrains Mono, 10–12px, uppercase, letter-spacing 1–2.5px, `var(--accent)`.
- Headings: weight 700–800, slight negative letter-spacing. Fluid sizes with `clamp()`.

### Layout

- Homepage sections: `max-width:1200px`, padding `100px 56px`.
- Blog articles: `.article-wrap` `max-width:720px`.
- Single mobile breakpoint: `@media(max-width:700px)`.
- Radii: 6–8px buttons, 10px cards, 12–14px large panels.

### Voice of the UI

Dark, minimal, engineering-flavoured. Numbered section labels (`01 — About`), one word of each
section title wrapped in `<span class="accent">`. No emoji in UI chrome. No stock icons — inline
SVG only (24×24 viewBox, stroke style).

## 4. Homepage (`index.html`)

### CONFIG block

Top of `<body>` holds a `CONFIG` object — change behaviour here, not in the markup:

```js
const CONFIG = {
  openToWork: false,  // shows the "Open to Opportunities" hero badge (keep false while employed)
  showHireMe: false,  // shows the "Hire Me" nav button (keep false while employed)
  email, linkedin, github, resumeUrl, medium,
};
```

### Sections and their patterns

| Section | id | Pattern to copy |
|---|---|---|
| Hero | `#hero` | Name reveal, typing line (`phrases` array in script), 3 `.hero-stats` |
| About | `#about` | `.about-text` paragraphs + 3 `.about-card`s |
| Experience | `#experience` | `.timeline` → `.tl-item` (newest first; only the first item gets `<span class="tl-badge">Current</span>`) |
| Projects | `#projects` | `.projects-grid` → `.proj-card` (icon, name, role, desc, tags, then `.proj-link` to a deep-dive **or** `.proj-soon`) |
| Skills | `#skills` | `.skills-grid` → `.skill-group` → `.skill-tag`s |
| Contact | `#contact` | Links rendered from CONFIG by script |

Section numbering (`01 — About` … `05 — Contact`) must stay sequential if sections are added/removed.

### Experience item format

```html
<div class="tl-item fade-up" style="transition-delay:.1s">
  <div class="tl-meta"><span class="tl-period">Mon YYYY — Mon YYYY</span></div>
  <div class="tl-role">Role</div>
  <div class="tl-company">Company, City</div>
  <div class="tl-stack">Tech · Tech · Tech</div>
  <ul class="tl-bullets" style="margin-top:14px">
    <li>Verb-first bullet with one <strong>key phrase</strong> and a real metric.</li>
  </ul>
</div>
```

Periods use `Mon YYYY — Present` / `Mon YYYY — Mon YYYY` with an em dash. Stagger `transition-delay` by `.1s` per item.

### Animations

`.fade-up` elements reveal via IntersectionObserver; hero items are revealed by the load handler.
New homepage blocks should get `class="fade-up"` and a small `transition-delay` stagger.

## 5. Blog

### Adding a new post — checklist

1. Copy `blog/_post-template.html` → `blog/<slug>.html`.
   - Slug: lowercase kebab-case, describes the topic, no dates (`context-engineering-agents`).
2. Replace every `{{PLACEHOLDER}}`; delete unused components and the instruction comment.
3. Fill **all** head tags: `<title>`, `description` (150–160 chars), `canonical`, `og:title`,
   `og:description`, `og:type=article`, `og:url`, `article:published_time`.
4. Cover image (optional) → `blog/images/<slug>-cover.png`, with meaningful `alt`.
5. In `blog/index.html`:
   - Add a `<a class="post-card">` block at the **top** of `#postGrid` (newest first).
   - Move `post-featured` + `<span class="featured-badge">Latest</span>` from the previous newest card to the new one.
   - Number the HTML comments sequentially (`<!-- ── POST 0 ── -->` = newest).
   - `data-tags`: space-separated from `ai | security | devops | dotnet` only. Add a new filter button if you add a new tag.
6. If the post is a deep-dive for a homepage project, set that `.proj-card`'s `.proj-link` to it.
7. Run the [Definition of done](#8-definition-of-done) checks.

### Post anatomy (canonical — see `_post-template.html`)

- `nav` with logo + "All articles" back link.
- `.article-meta` in this **fixed order**: `Category · Topic · Month YYYY · N min read`.
- `h1.article-title` — optional second line in `<span class="accent">`.
- `.article-subtitle` — the hook, 1–2 sentences.
- Optional `.hero-img`, optional `.toc` (use for posts over ~10 min; anchors match `h2` ids).
- `.prose` body; every `h2` has `data-num="01"`, `"02"`… (rendered as "01 —" above the heading).
- Components available: `.pull-quote`, `.code-block` (+ `.code-label`), `.callout`, `.two-col` with `.col-card.bad/.good`,
  `.result-grid` (exactly 3 `.result-card`s), `.table-wrap`, `.flow`/`.flow-step`, `.context-layers`, `.tag-row`.
- `.article-footer` author block (avatar "M", name, role, links: Medium · LinkedIn · More articles).
- Page `footer`.

### Conventions

- Dates: always `Month YYYY` in full (`September 2026`, not `Sep 2026`).
- Read time: ~230 words/minute, rounded. **The same number** must appear in the post meta and its index card.
- The card's `.post-title` and `.post-excerpt` must match the post's title and subtitle in substance.
- Code highlighting is manual spans inside `<pre>`: `.kw` `.str` `.attr` `.type` `.num` `.fn` `.comment`.
  Escape `<`, `>` and `&` in code samples.
- Links leaving the site: `target="_blank" rel="noopener"`.
- Internal links on blog pages are root-relative (`/blog/…`, `/`); homepage links are relative (`blog/`).

### Writing style (for drafting or editing post copy)

- First person, from things actually built and shipped. Problem → what I tried → what worked → results → takeaway.
- Short paragraphs, concrete examples, real numbers. Every metric needs one sentence on how it was measured.
- Headline formula used so far: a bold claim or a "I did X. Here's how/why." hook.
- No filler intros, no "In today's fast-paced world". No emoji in body text.
- Never change the meaning of the author's claims or invent metrics, quotes or results.
- Keep numbers consistent across the site (homepage stats, project cards, all posts).

## 6. Public repo rules

This repo is public. Do **not** commit:

- Secrets of any kind: API keys, tokens, connection strings, `.env` files.
- Private personal information beyond what is already intentionally public on the site
  (name, public email, LinkedIn, GitHub, city). No phone numbers, home address, IDs, salary,
  or personal/family details.
- Confidential employer or client material: internal code, repository names, customer or tenant
  names, internal URLs, architecture details not cleared for sharing, unpublished metrics.
  Write about **patterns and lessons**, with code samples rewritten as generic examples.
- Drafts that are not ready to be public (keep them outside the repo, or in a private repo).

If a requested change would violate these rules, stop and say so instead of committing.

## 7. LLM and search friendliness

- All meaningful content must exist in the static HTML. JavaScript may only **enhance**
  (animations, filters). Crawlers and LLMs often don't run JS — e.g. the resume link and the
  typing line on the homepage are currently JS-only.
- Use semantic elements (`nav`, `section`, `article`-like structure, one `h1` per page, ordered headings).
- Every image needs descriptive `alt` text.
- Keep `<title>`, `description` and Open Graph tags accurate whenever content changes.

## 8. Definition of done

Before finishing any change, verify:

- [ ] Opened the changed pages in a browser at desktop width and at ≤700px; nothing overflows.
- [ ] No unreplaced `{{` placeholders (`grep -rn "{{" --include=*.html .` should only match `_post-template.html`).
- [ ] All internal links resolve (blog cards ↔ post files, project deep-dive links, back links).
- [ ] Post meta, index card, and homepage agree on title, date, read time and any shared numbers.
- [ ] Design tokens unchanged, no new colours, fonts or dependencies.
- [ ] Nothing from the public repo rules was added.
- [ ] `CNAME` untouched.

## 9. Known inconsistencies (fix when touching these files)

- Agent pipeline size: `context-engineering-agents.html` says **17 agents**; homepage and
  `story-to-pr-pipeline.html` say **20**. Confirm the correct number with the author, then make it consistent.
- `story-to-pr-pipeline.html` meta says **6 min read**; its index card says **16 min read**.
- `.article-meta` order varies between posts; standardise to `Category · Topic · Month YYYY · N min read`.
- `microsoft-agent-framework-dotnet.html` uses `Aug 2026`; use `August 2026`.
- Author blocks differ (`.author-bio` vs `.article-footer`/`.author-row`, and one post has only a text sign-off);
  standardise on `.article-footer` from the template. Role lines are outdated in some posts.
- `blog/index.html`: two cards are commented `POST 3`; it also lacks `og:type`.
- Posts have no `canonical` or `article:published_time` tags yet.
