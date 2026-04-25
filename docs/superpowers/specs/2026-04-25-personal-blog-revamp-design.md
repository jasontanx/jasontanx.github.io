# Personal Blog Revamp — Design

**Date:** 2026-04-25
**Owner:** Jason Tan (`jasontanx` on GitHub)
**Status:** Approved (visual direction approved 2026-04-25; full spec pending user review)

---

## Goal

Replace the current Notion-hosted profile site (`ring-rifle-eee.notion.site/The-Data-Playground-My-Profile-...`) with a self-owned static site that:

- Lives on GitHub, deploys for free, owns its own URL.
- Reads as a personal hub rather than a long Notion document.
- Is structured to grow into a blog later without restructuring.

Total cost target: **$0**. Custom domain optional in the future (~$12/yr).

---

## Stack

| Layer | Choice | Why |
|---|---|---|
| Site generator | **Astro** | Modern, Markdown-first, gentle learning curve, strong free themes for personal sites, and easy to extend with components when needed. |
| Content format | **Markdown** in `src/content/` | Easy to edit, easy to migrate from Notion, version-controlled, no CMS or DB. |
| Hosting | **GitHub Pages** (free) | Already on GitHub; one-click hosting. |
| Deployment | **GitHub Actions** workflow | Push to `main` → site rebuilt and live in ~30s. No manual deploy. |
| Repo name | **`jasontanx.github.io`** (rename from `personal-blog`) | Gives clean root URL `https://jasontanx.github.io/` instead of `…/personal-blog/`. Still free. |
| Custom domain | Deferred (optional later) | Site can be reachable at a custom domain with a DNS change + GitHub config. ~$12/yr. |

Rejected alternatives: **Hugo** (theme customization clunkier than Astro components); **No-code platforms / Notion-as-CMS** (defeats the ownership/portability goal).

---

## Site Structure

V1 scope: 5 pages — hub-style homepage with sub-pages.

```
/             Home (hub)
              ├── Short intro paragraph
              ├── Three cards linking to: About · Career · Projects
              └── Contact links in footer

/about        About / Journey
              ├── Education path (Commerce → MBA → MSc Data Science)
              ├── Journey timeline (high-level visual arc — education + career
              │   transitions, no job-by-job detail)
              ├── Skills learned per semester (consolidated, tightened)
              └── Mode-of-learning + mode-of-work narrative

/career       Career (job-by-job detail, not on /about)
              ├── AirAsia (current, 2.5+ years) — featured at top
              │   └── ETL with Python, BigQuery, Airflow, SFTP, LLM exp.
              └── Huawei Malaysia (Management Trainee, until 2021)

/projects     Projects (one card per repo)
              ├── End-to-End Data Engineer Project
              ├── Terraform Practical
              ├── BigQuery to Postgres Ingestion
              └── Gsheet to BigQuery Ingestion

/contact      Contact
              ├── Email: jasontan9178@gmail.com
              └── LinkedIn · GitHub · Kaggle · Wordpress (link out)
```

### Decisions

- **Hub-first homepage** (not blog-first). Most existing content is evergreen profile material; a blog-first layout would bury it.
- **Drop the empty *"My Work"* section** (redundant with Projects + Career).
- **Merge *"Mission to Complete the Course"* into `/about`** as a sub-section. The original Notion structure had this split into two journey pages with overlapping content.
- **Defer to v2 (blog launch):** Knowledge Sharing articles (3 posts), *"Data Analytics Playground"*, *"Podcast LM Notebook"*. The `/blog` route is reserved in advance so adding it later doesn't require restructuring.
- **Wordpress posts at `thedatasciencejourney21.wordpress.com`:** not migrated in v1. Linked from Contact. Migration into `/blog` happens when v2 lands.

---

## Tech Architecture

```
jasontanx.github.io/                 (renamed repo)
├── src/
│   ├── pages/                       Routes — one .astro file per page
│   │   ├── index.astro              /
│   │   ├── about.astro              /about
│   │   ├── career.astro             /career
│   │   ├── projects.astro           /projects
│   │   └── contact.astro            /contact
│   ├── layouts/
│   │   └── BaseLayout.astro         Shared header/footer/nav
│   ├── components/
│   │   ├── Nav.astro
│   │   ├── ProjectCard.astro
│   │   └── TimelineItem.astro
│   └── content/                     Markdown content
│       ├── about.md
│       ├── career-airasia.md
│       └── projects/*.md            One MD file per project
├── public/
│   └── images/                      Rehosted images (committed)
├── astro.config.mjs
├── package.json
└── .github/workflows/deploy.yml     Auto-deploy on push to main
```

**Boundaries:**

- **Content** (Markdown in `src/content/`) — pure prose, easy to edit, no logic.
- **Layout & components** (`.astro` files in `src/layouts/`, `src/components/`) — pure presentation, no content.
- **Pages** (`src/pages/*.astro`) — thin glue: pull content, wrap in layout, render.

This split means content edits never require touching code, and layout changes never risk breaking content.

---

## Visual Direction

**Selected: B — Modern / Bold**

- Sans-serif typography (system stack or Inter)
- Subtle gradient hero on homepage
- Indigo accent color for links and highlights
- Card-based section navigation
- Light theme by default; dark mode can be added later
- Polished, current, reads as a credible 2026 developer portfolio

Rejected directions: A (Editorial / Minimalist serif) — too quiet for a portfolio; C (Tech / Terminal) — too strong an aesthetic commitment, narrows audience.

---

## Content Migration Plan

Source: `content.csv` (128 lines) at the project root. **Keep `content.csv` as a backup** after migration completes.

### 1. Image rehosting *(required)*

All images currently point to `secure.notion-static.com/…` URLs that will eventually 404. Five images to download:

- `Career_Timeline.png`
- `Mode of learning.png`
- `Mode of work.png`
- `skills1.png`
- `skills2.png`

**Naming rule:** rename any image with spaces to use underscores (e.g. `Mode of learning.png` → `Mode_of_learning.png`). Save under `public/images/`, update markdown to reference `/images/...`.

### 2. Content split

| Source section in `content.csv` | Target |
|---|---|
| "The Journey Begins - About Me" + "Mission to Complete the Course" | `src/content/about.md` |
| "My Journey in AirAsia (so far…)!" | `src/content/career-airasia.md` |
| "My Data Engineering Projects" | `src/content/projects/*.md` (one per repo) |
| "Knowledge Sharing - Everything about Data" | Deferred to v2 |
| "Contact Me" | Inlined into `src/pages/contact.astro` |

### 3. Narrative polish *(approved)*

Tighten phrasing where the source reads CV-like. Example:
- *Before:* "I have to adapt in utilising video conferencing tools like Webex and Moodle."
- *After:* "Lectures moved to Webex and Moodle overnight."

User reviews diff after polish.

### 4. Wordpress posts

Skip in v1. Link Wordpress URL from `/contact`. Migrate into `/blog` in v2.

---

## Deployment

- GitHub Actions workflow at `.github/workflows/deploy.yml`.
- Trigger: push to `main`.
- Steps: install dependencies → `astro build` → publish `dist/` to GitHub Pages.
- Initial URL: `https://jasontanx.github.io/`.
- Build time target: under 60s end-to-end.

---

## Out of Scope (v1)

- Blog / articles section (route reserved, content deferred to v2)
- Comments
- Newsletter signup
- Search
- Analytics
- Custom domain
- Dark mode
- RSS feed
- Wordpress post migration

These are all easy adds in v2 once the foundation ships.

---

## Success Criteria

V1 is shippable when:

1. Site is live at `https://jasontanx.github.io/` with all five pages rendering.
2. All five images are rehosted and load from `/images/`.
3. No links point to `notion.site` or `secure.notion-static.com`.
4. Pushing to `main` automatically deploys within ~60s.
5. `content.csv` is preserved at the repo root as a backup.
