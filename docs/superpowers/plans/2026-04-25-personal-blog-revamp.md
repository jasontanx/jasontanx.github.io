# Personal Blog Revamp Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and ship a static personal site at `https://jasontanx.github.io/` to replace the current Notion profile, using Astro + GitHub Pages.

**Architecture:** Astro static site with Markdown content under `src/content/`, plain-CSS theming (no Tailwind), and a GitHub Actions deploy on push-to-main. Five pages in v1 (Home, About, Career, Projects, Contact). No content collections — content is loaded via direct Markdown imports for single-doc pages and `import.meta.glob` for the project list — keeping ceremony low for v1.

**Tech Stack:** Astro (latest), Markdown, plain CSS with custom properties, GitHub Pages, GitHub Actions.

**Spec:** `docs/superpowers/specs/2026-04-25-personal-blog-revamp-design.md`

**Working dir:** `/Users/junshengtan/Desktop/personal-blog/`

---

## Notes for the executor

- **No tests in the traditional sense.** This is a static content site with no business logic. "Verification" = running the dev server / build and visually confirming what's rendered. Each task ends with a verification step before commit.
- **Frequent commits.** Every task ends with a commit. Don't batch commits across tasks.
- **No `--no-verify`, no force-pushes.** Standard git hygiene.
- **The dir is currently NOT a git repo and NOT linked to GitHub.** Task 1 fixes that.
- **`content.csv` at repo root is the source of truth for migration content. Do not delete it.** It stays as a backup per spec.

---

## Task 1: Initialize git repo and project metadata

**Files:**
- Create: `.gitignore`
- Create: `README.md`

- [ ] **Step 1: Initialize git in the project directory**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git init -b main
```

Expected: `Initialized empty Git repository in /Users/junshengtan/Desktop/personal-blog/.git/`

- [ ] **Step 2: Create `.gitignore`**

Write to `/Users/junshengtan/Desktop/personal-blog/.gitignore`:

```gitignore
# build output
dist/
.astro/

# dependencies
node_modules/

# logs
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*

# environment
.env
.env.production

# macOS
.DS_Store

# editors
.vscode/
.idea/

# brainstorming workspace (per superpowers convention)
.superpowers/
```

- [ ] **Step 3: Create a minimal README.md**

Write to `/Users/junshengtan/Desktop/personal-blog/README.md` (note: the README contains real triple-backtick fenced code blocks — write them literally, do **not** escape them):

````markdown
# jasontanx.github.io

Personal site — built with [Astro](https://astro.build/), hosted on GitHub Pages.

## Develop

```bash
npm install
npm run dev      # http://localhost:4321
npm run build    # ./dist
npm run preview  # preview prod build
```

## Deploy

Pushing to `main` triggers `.github/workflows/deploy.yml` which builds and publishes to GitHub Pages.
````

- [ ] **Step 4: First commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add .gitignore README.md content.csv docs/
git commit -m "chore: initial repo with spec, plan, and content backup"
```

Expected: One commit on `main` containing the spec, plan, gitignore, README, and content.csv.

---

## Task 2: Bootstrap Astro project

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tsconfig.json`, `src/`, `public/`

- [ ] **Step 1: Run the Astro create wizard with explicit defaults**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm create astro@latest . -- --template minimal --no-install --no-git --typescript strict --yes
```

Notes for the executor:
- The `.` runs the wizard in the current directory.
- `--template minimal` gives an empty project — we'll add pages ourselves.
- `--no-install` so we control when `npm install` runs.
- `--no-git` since the repo is already initialized.
- If the wizard prompts about an existing non-empty directory, choose **Continue**.

Expected: New files in current dir: `package.json`, `astro.config.mjs`, `tsconfig.json`, `src/pages/index.astro`, `public/favicon.svg`. Existing files (`.gitignore`, `README.md`, `content.csv`, `docs/`) untouched.

- [ ] **Step 2: Install dependencies**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm install
```

Expected: `node_modules/` populated, `package-lock.json` created. No errors.

- [ ] **Step 3: Verify dev server starts**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run dev
```

Open `http://localhost:4321/` in a browser — should show the default Astro starter page. Stop the server (Ctrl+C).

- [ ] **Step 4: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add package.json package-lock.json astro.config.mjs tsconfig.json src/ public/
git commit -m "chore: bootstrap Astro minimal template"
```

---

## Task 3: Set up global CSS theme (Modern / Bold direction)

**Files:**
- Create: `src/styles/global.css`

- [ ] **Step 1: Create `src/styles/global.css`**

Write to `/Users/junshengtan/Desktop/personal-blog/src/styles/global.css`:

```css
:root {
  --color-bg: #ffffff;
  --color-bg-alt: #f5f7ff;
  --color-text: #0f172a;
  --color-text-muted: #475569;
  --color-border: #e2e8f0;
  --color-accent: #6366f1;
  --color-accent-hover: #4f46e5;
  --font-sans: -apple-system, BlinkMacSystemFont, "Segoe UI", "Inter", system-ui, sans-serif;
  --font-mono: "JetBrains Mono", "SF Mono", Menlo, monospace;
  --max-width: 760px;
  --radius: 8px;
}

* { box-sizing: border-box; }

html, body {
  margin: 0;
  padding: 0;
  font-family: var(--font-sans);
  color: var(--color-text);
  background: var(--color-bg);
  line-height: 1.6;
  -webkit-font-smoothing: antialiased;
}

main {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: 48px 24px 80px;
}

h1 {
  font-size: 2.4rem;
  font-weight: 800;
  letter-spacing: -0.02em;
  margin: 0 0 8px;
  line-height: 1.15;
}

h2 {
  font-size: 1.6rem;
  font-weight: 700;
  letter-spacing: -0.01em;
  margin: 40px 0 12px;
}

h3 {
  font-size: 1.2rem;
  font-weight: 700;
  margin: 28px 0 8px;
}

p, ul, ol { margin: 0 0 16px; }

a {
  color: var(--color-accent);
  text-decoration: none;
  border-bottom: 1px solid transparent;
  transition: border-color 0.15s ease, color 0.15s ease;
}

a:hover {
  color: var(--color-accent-hover);
  border-bottom-color: currentColor;
}

img {
  max-width: 100%;
  height: auto;
  border-radius: var(--radius);
  margin: 16px 0;
}

code {
  font-family: var(--font-mono);
  font-size: 0.92em;
  background: var(--color-bg-alt);
  padding: 2px 6px;
  border-radius: 4px;
}

.subtitle {
  color: var(--color-text-muted);
  font-size: 1.1rem;
  margin-bottom: 32px;
}

.tag {
  display: inline-block;
  font-size: 0.78rem;
  background: var(--color-bg-alt);
  color: var(--color-accent);
  padding: 2px 8px;
  border-radius: 999px;
  margin: 0 4px 4px 0;
  font-weight: 500;
}
```

- [ ] **Step 2: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/styles/global.css
git commit -m "feat: add global CSS theme (modern/bold)"
```

---

## Task 4: Create Nav and Footer components

**Files:**
- Create: `src/components/Nav.astro`
- Create: `src/components/Footer.astro`

- [ ] **Step 1: Create `src/components/Nav.astro`**

```astro
---
const { pathname } = Astro.url;
const links = [
  { href: "/", label: "Home" },
  { href: "/about/", label: "About" },
  { href: "/career/", label: "Career" },
  { href: "/projects/", label: "Projects" },
  { href: "/contact/", label: "Contact" },
];
---

<nav class="nav">
  <a href="/" class="brand">Jason Tan</a>
  <ul>
    {links.filter((l) => l.href !== "/").map((l) => (
      <li>
        <a
          href={l.href}
          class:list={[{ active: pathname === l.href || pathname === l.href.replace(/\/$/, "") }]}
        >{l.label}</a>
      </li>
    ))}
  </ul>
</nav>

<style>
  .nav {
    max-width: var(--max-width);
    margin: 0 auto;
    padding: 24px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid var(--color-border);
  }
  .brand {
    font-weight: 800;
    font-size: 1.05rem;
    color: var(--color-text);
    border-bottom: none;
  }
  ul {
    display: flex;
    gap: 18px;
    margin: 0;
    padding: 0;
    list-style: none;
  }
  ul a {
    color: var(--color-text-muted);
    font-size: 0.95rem;
    border-bottom: none;
  }
  ul a.active,
  ul a:hover {
    color: var(--color-accent);
  }
  @media (max-width: 560px) {
    .nav { flex-direction: column; gap: 12px; align-items: flex-start; }
    ul { flex-wrap: wrap; gap: 12px; }
  }
</style>
```

- [ ] **Step 2: Create `src/components/Footer.astro`**

```astro
---
const year = new Date().getFullYear();
---

<footer>
  <div>
    <a href="https://www.linkedin.com/in/junshengtan/">LinkedIn</a> ·
    <a href="https://github.com/jasontanx">GitHub</a> ·
    <a href="https://www.kaggle.com/tanjunshengjason">Kaggle</a>
  </div>
  <small>&copy; {year} Jason Tan</small>
</footer>

<style>
  footer {
    max-width: var(--max-width);
    margin: 0 auto;
    padding: 32px 24px;
    border-top: 1px solid var(--color-border);
    color: var(--color-text-muted);
    font-size: 0.9rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 12px;
  }
</style>
```

- [ ] **Step 3: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/components/Nav.astro src/components/Footer.astro
git commit -m "feat: add Nav and Footer components"
```

---

## Task 5: Create BaseLayout

**Files:**
- Create: `src/layouts/BaseLayout.astro`

- [ ] **Step 1: Create `src/layouts/BaseLayout.astro`**

```astro
---
import "../styles/global.css";
import Nav from "../components/Nav.astro";
import Footer from "../components/Footer.astro";

interface Props {
  title: string;
  description?: string;
}

const { title, description = "Jason Tan — Data Engineer at AirAsia." } = Astro.props;
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <title>{title}</title>
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
  </head>
  <body>
    <Nav />
    <main>
      <slot />
    </main>
    <Footer />
  </body>
</html>
```

- [ ] **Step 2: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/layouts/BaseLayout.astro
git commit -m "feat: add BaseLayout"
```

---

## Task 6: Replace default home page with hub layout

**Files:**
- Modify: `src/pages/index.astro` (overwrite the Astro starter content)

- [ ] **Step 1: Overwrite `src/pages/index.astro`**

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
---

<BaseLayout title="Jason Tan — Data Engineer">
  <section class="hero">
    <h1>Jason Tan</h1>
    <p class="subtitle">Data Engineer @ AirAsia · ex-Huawei. Business graduate turned data engineer — building ETL pipelines, documenting the journey.</p>
  </section>

  <section class="cards">
    <a class="card" href="/about/">
      <h3>About</h3>
      <p>The journey from a Bachelor of Commerce to MSc Data Science.</p>
    </a>
    <a class="card" href="/career/">
      <h3>Career</h3>
      <p>2.5+ years building ETL at AirAsia, plus what came before.</p>
    </a>
    <a class="card" href="/projects/">
      <h3>Projects</h3>
      <p>Personal data engineering projects on GitHub.</p>
    </a>
  </section>
</BaseLayout>

<style>
  .hero {
    padding: 32px 0 24px;
    background: linear-gradient(135deg, var(--color-bg-alt) 0%, transparent 70%);
    border-radius: var(--radius);
    margin-bottom: 32px;
    padding: 32px 24px;
  }
  .cards {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 16px;
  }
  .card {
    border: 1px solid var(--color-border);
    border-radius: var(--radius);
    padding: 20px;
    color: var(--color-text);
    transition: border-color 0.15s ease, transform 0.15s ease;
  }
  .card:hover {
    border-color: var(--color-accent);
    transform: translateY(-2px);
  }
  .card h3 {
    margin: 0 0 6px;
    color: var(--color-accent);
  }
  .card p {
    margin: 0;
    color: var(--color-text-muted);
    font-size: 0.95rem;
  }
  @media (max-width: 720px) {
    .cards { grid-template-columns: 1fr; }
  }
</style>
```

- [ ] **Step 2: Verify the home page renders**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run dev
```

Visit `http://localhost:4321/`. Confirm: name and subtitle visible at top, three cards (About / Career / Projects), nav at top, footer at bottom. The other pages (About/Career/etc.) will 404 — that's expected, we add them next. Stop the server.

- [ ] **Step 3: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/pages/index.astro
git commit -m "feat: replace starter home page with hub layout"
```

---

## Task 7: Create page scaffolds (About / Career / Projects / Contact)

**Files:**
- Create: `src/pages/about.astro`
- Create: `src/pages/career.astro`
- Create: `src/pages/projects.astro`
- Create: `src/pages/contact.astro`

These are placeholder scaffolds — content gets wired in later tasks. Goal here: all five routes resolve and the nav highlights the right link.

- [ ] **Step 1: Create `src/pages/about.astro`**

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
---

<BaseLayout title="About — Jason Tan">
  <h1>About</h1>
  <p class="subtitle">Coming soon.</p>
</BaseLayout>
```

- [ ] **Step 2: Create `src/pages/career.astro`**

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
---

<BaseLayout title="Career — Jason Tan">
  <h1>Career</h1>
  <p class="subtitle">Coming soon.</p>
</BaseLayout>
```

- [ ] **Step 3: Create `src/pages/projects.astro`**

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
---

<BaseLayout title="Projects — Jason Tan">
  <h1>Projects</h1>
  <p class="subtitle">Coming soon.</p>
</BaseLayout>
```

- [ ] **Step 4: Create `src/pages/contact.astro`**

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
---

<BaseLayout title="Contact — Jason Tan">
  <h1>Contact</h1>
  <p class="subtitle">Thanks for stopping by — feel free to reach out.</p>

  <ul class="contact-links">
    <li>📧 <a href="mailto:jasontan9178@gmail.com">jasontan9178@gmail.com</a></li>
    <li>💼 <a href="https://www.linkedin.com/in/junshengtan/">LinkedIn</a></li>
    <li>🐱 <a href="https://github.com/jasontanx">GitHub</a></li>
    <li>🥇 <a href="https://www.kaggle.com/tanjunshengjason">Kaggle</a></li>
    <li>✨ <a href="https://thedatasciencejourney21.wordpress.com/">Older blog (Wordpress)</a></li>
  </ul>
</BaseLayout>

<style>
  .contact-links { list-style: none; padding: 0; }
  .contact-links li { margin: 8px 0; font-size: 1.05rem; }
</style>
```

- [ ] **Step 5: Verify all five routes render**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run dev
```

Visit each: `/`, `/about/`, `/career/`, `/projects/`, `/contact/`. Confirm: each loads, nav highlights the active link, contact page shows working email + social links. Stop the server.

- [ ] **Step 6: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/pages/about.astro src/pages/career.astro src/pages/projects.astro src/pages/contact.astro
git commit -m "feat: scaffold About/Career/Projects/Contact pages"
```

---

## Task 8: Download and rehost images

**Files:**
- Create: `public/images/Career_Timeline.png`
- Create: `public/images/Mode_of_learning.png`
- Create: `public/images/Mode_of_work.png`
- Create: `public/images/skills1.png`
- Create: `public/images/skills2.png`

Source URLs (from `content.csv`):

| Filename (target) | Source URL |
|---|---|
| `Career_Timeline.png` | `https://s3-us-west-2.amazonaws.com/secure.notion-static.com/841d1189-6ce5-4766-b742-e23f5d2ae73d/Career_Timeline.png` |
| `Mode_of_learning.png` | `https://s3-us-west-2.amazonaws.com/secure.notion-static.com/78ee3663-c8b3-4a62-bd03-0299755ff9b4/Mode_of_learning.png` |
| `Mode_of_work.png` | `https://s3-us-west-2.amazonaws.com/secure.notion-static.com/806fbf37-a752-4f7f-bbb0-a1f4cd72983b/Mode_of_work.png` |
| `skills1.png` | `https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3042f286-6ea5-4046-84e8-b74fddcfe2af/skills1.png` |
| `skills2.png` | `https://s3-us-west-2.amazonaws.com/secure.notion-static.com/878acfe9-bcbf-4cab-977f-1d82da4c38a9/skills2.png` |

- [ ] **Step 1: Make the images directory**

```bash
mkdir -p /Users/junshengtan/Desktop/personal-blog/public/images
```

- [ ] **Step 2: Try to download all five images**

```bash
cd /Users/junshengtan/Desktop/personal-blog/public/images

curl -fsSL -o Career_Timeline.png "https://s3-us-west-2.amazonaws.com/secure.notion-static.com/841d1189-6ce5-4766-b742-e23f5d2ae73d/Career_Timeline.png" || echo "FAIL Career_Timeline.png"
curl -fsSL -o Mode_of_learning.png "https://s3-us-west-2.amazonaws.com/secure.notion-static.com/78ee3663-c8b3-4a62-bd03-0299755ff9b4/Mode_of_learning.png" || echo "FAIL Mode_of_learning.png"
curl -fsSL -o Mode_of_work.png "https://s3-us-west-2.amazonaws.com/secure.notion-static.com/806fbf37-a752-4f7f-bbb0-a1f4cd72983b/Mode_of_work.png" || echo "FAIL Mode_of_work.png"
curl -fsSL -o skills1.png "https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3042f286-6ea5-4046-84e8-b74fddcfe2af/skills1.png" || echo "FAIL skills1.png"
curl -fsSL -o skills2.png "https://s3-us-west-2.amazonaws.com/secure.notion-static.com/878acfe9-bcbf-4cab-977f-1d82da4c38a9/skills2.png" || echo "FAIL skills2.png"

ls -la
```

- [ ] **Step 3: If any image failed (FAIL line printed)**

The Notion S3 URLs require a signed query string and frequently 404 once the page loses signing context. **If any download failed, stop here and ask the user** to manually export those specific images from the existing Notion page (right-click → save image, or Notion → Export → Markdown & CSV which bundles assets), then save them into `public/images/` with the exact filenames above. Continue once all five files exist.

For any image that's still missing after the user's effort, leave the file out and remove the corresponding `![…](/images/…)` line from the markdown in Task 11 — the page should not link to a non-existent image.

- [ ] **Step 4: Verify all five images are present**

```bash
ls /Users/junshengtan/Desktop/personal-blog/public/images
```

Expected: exactly five `.png` files matching the names above (or fewer if some couldn't be recovered, with an explicit list of the missing ones).

- [ ] **Step 5: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add public/images/
git commit -m "feat: rehost images from Notion to /public/images"
```

---

## Task 9: Migrate About content (with narrative polish)

**Files:**
- Create: `src/content/about.md`
- Modify: `src/pages/about.astro`

This combines the source sections "The Journey Begins - About Me" and "My Journey - Mission to Complete the Course" into one polished About page. Narrative is tightened from CV-style to confident first-person.

- [ ] **Step 1: Create `src/content/about.md`**

```markdown
# About

A business graduate turned data engineer.

## Background

I started in business — a Bachelor of Commerce (International Business) — and pursued my MBA at INTI College Subang in collaboration with Coventry University, UK, while working as a Management Associate at a Singapore-based startup.

When my Management Trainee contract at Huawei Malaysia wrapped up in 2021, I made the jump to data. In August 2021 I enrolled in the MSc Data Science and Business Analytics program at Asia Pacific University (APU) full-time.

## The Pivot

The COVID-19 pandemic reshaped both how I learned and how I worked. My Diploma and Degree happened on campus; halfway through my MBA, classes moved fully online — Webex and Moodle overnight. The MSc was largely remote too, mostly over Microsoft Teams.

Same story at work. Most of 2021 was work-from-home. Hybrid arrangements followed when restrictions eased. I've now worked fully physical, fully remote, and everything in between — turns out the routine matters less than the communication tooling.

## Career & Education Timeline

![Career Timeline](/images/Career_Timeline.png)

Five years across three milestones in higher education and a handful of roles across multiple companies.

## Mode of Learning

![Mode of Learning](/images/Mode_of_learning.png)

## Mode of Work

![Mode of Work](/images/Mode_of_work.png)

## Skills Acquired in MSc Data Science

### Year 1 — First Semester (2021)

![Year 1 Skills](/images/skills1.png)

- **R Programming** — predictive modeling
- **SAP Lumira** — data visualization (Business Intelligence module)
- **SAS Studio** — Data Manipulation Language (DML) and data management

### Year 2 — Second & Final Semesters (2022)

![Year 2 Skills](/images/skills2.png)

- **Tableau** and **SAS Enterprise Miner** — analytics projects
- **SPSS** — statistical methods (t-test, ANOVA, chi-square)
- **Cloudcraft** — cloud architecture diagrams
- **Python** (Final semester) — NLP and deep learning, mostly via Google Colab
```

- [ ] **Step 2: Wire `about.md` into `src/pages/about.astro`**

Replace `/Users/junshengtan/Desktop/personal-blog/src/pages/about.astro` with:

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
import { Content } from "../content/about.md";
---

<BaseLayout title="About — Jason Tan">
  <Content />
</BaseLayout>
```

- [ ] **Step 3: Verify the About page renders**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run dev
```

Visit `http://localhost:4321/about/`. Confirm: heading "About", subheadings render, all five images load (no broken-image icons). Stop the server.

If any image is broken, double-check that `public/images/<filename>.png` exists and the markdown reference matches case-sensitively.

- [ ] **Step 4: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/content/about.md src/pages/about.astro
git commit -m "feat: migrate About content with narrative polish"
```

---

## Task 10: Migrate Career content (AirAsia + Huawei)

**Files:**
- Create: `src/content/career-airasia.md`
- Create: `src/content/career-huawei.md`
- Modify: `src/pages/career.astro`

- [ ] **Step 1: Create `src/content/career-airasia.md`**

```markdown
## AirAsia — Data Engineer

**2.5+ years and counting.**

I joined AirAsia as a Data Engineer and have spent the time so far building and scheduling ETL pipelines that move data between operational systems, vendor sources, and our data warehouse.

### What I've built

- **Google Sheets → BigQuery ingestion.** Python ETL scripts that extract from Google Sheets and load into BigQuery on a schedule.
- **Database → Warehouse pipelines.** Extracted data from Postgres, MS SQL, and other operational databases into the data warehouse.
- **Stored Procedure → BigQuery.** ETL code that executes a Stored Procedure on the source system and transfers the result into BigQuery.
- **Airflow scheduling.** Authored DAGs in Apache Airflow to schedule and monitor the pipelines above.
- **SFTP ingestion.** Picked up files from SFTP, applied transformations per spec, loaded the cleaned data into BigQuery.
- **BigQuery → SFTP exports.** Reverse direction — extracted query results from BigQuery and dropped them at specific SFTP paths as CSV for downstream consumers.
- **Documentation.** Documented every pipeline I shipped on the company Confluence.
- **LLM experimentation.** Explored how ChatGPT and Gemini can speed up ETL development — generating boilerplate, debugging schema mismatches, drafting documentation.

Outside of work I also build personal projects to go deeper on the tooling — see [Projects](/projects/).
```

- [ ] **Step 2: Create `src/content/career-huawei.md`**

```markdown
## Huawei Malaysia — Management Trainee

**Until 2021.**

A rotational management trainee program at Huawei Malaysia. The contract wrapped in 2021, and that was the moment I made the transition into data.
```

- [ ] **Step 3: Wire both into `src/pages/career.astro`**

Replace `/Users/junshengtan/Desktop/personal-blog/src/pages/career.astro` with:

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
import { Content as AirAsia } from "../content/career-airasia.md";
import { Content as Huawei } from "../content/career-huawei.md";
---

<BaseLayout title="Career — Jason Tan">
  <h1>Career</h1>
  <p class="subtitle">Roles and what I built in each.</p>

  <article>
    <AirAsia />
  </article>

  <article>
    <Huawei />
  </article>
</BaseLayout>

<style>
  article {
    border-top: 1px solid var(--color-border);
    padding-top: 24px;
    margin-top: 24px;
  }
  article:first-of-type {
    border-top: none;
    padding-top: 0;
    margin-top: 0;
  }
</style>
```

- [ ] **Step 4: Verify the Career page renders**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run dev
```

Visit `http://localhost:4321/career/`. Confirm: AirAsia section appears first with the bulleted list of work, Huawei follows below a divider, the link to `/projects/` at the end of the AirAsia section works. Stop the server.

- [ ] **Step 5: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/content/career-airasia.md src/content/career-huawei.md src/pages/career.astro
git commit -m "feat: migrate Career content (AirAsia + Huawei)"
```

---

## Task 11: Migrate Projects content

**Files:**
- Create: `src/content/projects/data-engineer-project.md`
- Create: `src/content/projects/terraform-practice.md`
- Create: `src/content/projects/bq-to-postgres.md`
- Create: `src/content/projects/gsheet-to-bq.md`

Each project file uses YAML frontmatter so the Projects page can render cards from the metadata (title, repo URL, tags) plus a short description body.

- [ ] **Step 1: Create `src/content/projects/data-engineer-project.md`**

```markdown
---
title: End-to-End Data Engineer Project
github: https://github.com/jasontanx/data-engineer-project-1
tags: [Python, ETL, End-to-End]
order: 1
---

A full end-to-end data engineering pipeline built from scratch — extract, transform, load, schedule. A sandbox for practicing the entire pipeline lifecycle outside of work.
```

- [ ] **Step 2: Create `src/content/projects/terraform-practice.md`**

```markdown
---
title: Terraform Practical
github: https://github.com/jasontanx/terraform-practice
tags: [Terraform, IaC]
order: 2
---

Hands-on practice with Terraform — provisioning, modules, and infrastructure-as-code patterns I wanted to get comfortable with before reaching for them in production.
```

- [ ] **Step 3: Create `src/content/projects/bq-to-postgres.md`**

```markdown
---
title: BigQuery to Postgres Ingestion
github: https://github.com/jasontanx/bigquery-to-postgres-etl
tags: [Python, BigQuery, Postgres, ETL]
order: 3
---

Reverse-direction ETL — pulling data out of BigQuery and into a Postgres database. Useful for sharing query results with services that want a relational store rather than a warehouse.
```

- [ ] **Step 4: Create `src/content/projects/gsheet-to-bq.md`**

```markdown
---
title: Google Sheets to BigQuery Ingestion
github: https://github.com/jasontanx/gsheet-to-bq-ingestion
tags: [Python, Google Sheets, BigQuery, ETL]
order: 4
---

A reusable pipeline pattern for ingesting data from Google Sheets into BigQuery — handles auth, schema, and incremental loads.
```

- [ ] **Step 5: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/content/projects/
git commit -m "feat: add Projects markdown content (4 projects)"
```

---

## Task 12: Build the Projects page (with ProjectCard component)

**Files:**
- Create: `src/components/ProjectCard.astro`
- Modify: `src/pages/projects.astro`

- [ ] **Step 1: Create `src/components/ProjectCard.astro`**

```astro
---
interface Props {
  title: string;
  github: string;
  tags: string[];
  description: string;
}

const { title, github, tags, description } = Astro.props;
---

<article class="project">
  <h3><a href={github}>{title}</a></h3>
  <p>{description}</p>
  <div class="tags">
    {tags.map((t) => <span class="tag">{t}</span>)}
  </div>
</article>

<style>
  .project {
    border: 1px solid var(--color-border);
    border-radius: var(--radius);
    padding: 20px;
    margin-bottom: 16px;
    transition: border-color 0.15s ease;
  }
  .project:hover {
    border-color: var(--color-accent);
  }
  .project h3 {
    margin: 0 0 8px;
  }
  .project h3 a {
    border-bottom: none;
  }
  .project p {
    margin: 0 0 12px;
    color: var(--color-text-muted);
  }
  .tags { display: flex; flex-wrap: wrap; }
</style>
```

- [ ] **Step 2: Replace `src/pages/projects.astro`**

```astro
---
import BaseLayout from "../layouts/BaseLayout.astro";
import ProjectCard from "../components/ProjectCard.astro";

interface ProjectFrontmatter {
  title: string;
  github: string;
  tags: string[];
  order: number;
}

type ProjectModule = {
  frontmatter: ProjectFrontmatter;
  rawContent: () => string;
};

const projectModules = import.meta.glob<ProjectModule>("../content/projects/*.md", { eager: true });

const projects = Object.values(projectModules)
  .map((mod) => ({
    ...mod.frontmatter,
    description: mod.rawContent().replace(/^---[\s\S]*?---/, "").trim(),
  }))
  .sort((a, b) => a.order - b.order);
---

<BaseLayout title="Projects — Jason Tan">
  <h1>Projects</h1>
  <p class="subtitle">Personal projects I've built outside of work to go deeper on the tooling.</p>

  {projects.map((p) => (
    <ProjectCard
      title={p.title}
      github={p.github}
      tags={p.tags}
      description={p.description}
    />
  ))}
</BaseLayout>
```

- [ ] **Step 3: Verify the Projects page renders**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run dev
```

Visit `http://localhost:4321/projects/`. Confirm: four project cards in order (End-to-End, Terraform, BQ-to-Postgres, GSheet-to-BQ), each title is a link to the matching GitHub repo, tags appear as pills, descriptions match the markdown bodies. Stop the server.

- [ ] **Step 4: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add src/components/ProjectCard.astro src/pages/projects.astro
git commit -m "feat: build Projects page with cards from markdown"
```

---

## Task 13: Production build verification

Before configuring deployment, confirm the entire site builds clean.

- [ ] **Step 1: Run a clean production build**

```bash
cd /Users/junshengtan/Desktop/personal-blog
rm -rf dist/
npm run build
```

Expected: build completes with no errors. Output mentions building 5 routes (`/`, `/about/`, `/career/`, `/projects/`, `/contact/`).

- [ ] **Step 2: Inspect the build output**

```bash
ls /Users/junshengtan/Desktop/personal-blog/dist/
```

Expected: directory contains `index.html`, `about/`, `career/`, `projects/`, `contact/`, `images/`, plus an `_astro/` directory with bundled assets.

- [ ] **Step 3: Preview the production build**

```bash
cd /Users/junshengtan/Desktop/personal-blog
npm run preview
```

Visit `http://localhost:4321/` and click through every page (Home → About → Career → Projects → Contact). Confirm: every page loads, all five images on About load, all four project cards show on Projects, all contact links work. Stop the preview server.

- [ ] **Step 4: Commit (if anything changed)**

If the build introduced any tracked file changes, commit them. Otherwise skip.

```bash
cd /Users/junshengtan/Desktop/personal-blog
git status
```

If `git status` shows changes (it shouldn't — `dist/` and `.astro/` are gitignored):

```bash
git add -A
git commit -m "chore: post-build artifacts"
```

---

## Task 14: Configure Astro for GitHub Pages deployment

**Files:**
- Modify: `astro.config.mjs`

Because the repo will be renamed to `jasontanx.github.io` (GitHub user-site repo), the site is served at `https://jasontanx.github.io/` with **no path prefix**. So we set `site` but leave `base` at default.

- [ ] **Step 1: Replace `astro.config.mjs`**

Overwrite `/Users/junshengtan/Desktop/personal-blog/astro.config.mjs` with:

```javascript
import { defineConfig } from "astro/config";

export default defineConfig({
  site: "https://jasontanx.github.io",
  // base: "/", // default — keep root since the repo will be named jasontanx.github.io
  build: {
    assets: "_astro",
  },
});
```

- [ ] **Step 2: Verify the build still works**

```bash
cd /Users/junshengtan/Desktop/personal-blog
rm -rf dist/
npm run build
```

Expected: clean build, no errors.

- [ ] **Step 3: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add astro.config.mjs
git commit -m "chore: configure site for jasontanx.github.io"
```

---

## Task 15: Add GitHub Actions deploy workflow

**Files:**
- Create: `.github/workflows/deploy.yml`

- [ ] **Step 1: Create the workflow file**

```bash
mkdir -p /Users/junshengtan/Desktop/personal-blog/.github/workflows
```

Write to `/Users/junshengtan/Desktop/personal-blog/.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Commit**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add .github/workflows/deploy.yml
git commit -m "ci: add GitHub Pages deploy workflow"
```

---

## Task 16: Create the GitHub repo and push

This task involves the user's GitHub account. The executor should perform the steps via the `gh` CLI if logged in; otherwise, instruct the user.

- [ ] **Step 1: Check `gh` auth**

```bash
gh auth status
```

If not logged in: instruct the user to run `gh auth login` interactively (this is an authenticated action that should not be performed silently). Pause until the user confirms login.

- [ ] **Step 2: Create the repo as `jasontanx.github.io`**

```bash
cd /Users/junshengtan/Desktop/personal-blog
gh repo create jasontanx.github.io --public --source=. --remote=origin --description "Personal site — Astro on GitHub Pages"
```

Expected: repo created at `https://github.com/jasontanx/jasontanx.github.io`, remote `origin` configured.

- [ ] **Step 3: Push main**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git push -u origin main
```

Expected: push succeeds. The deploy workflow starts automatically.

- [ ] **Step 4: Enable GitHub Pages with "GitHub Actions" source**

```bash
gh api -X POST /repos/jasontanx/jasontanx.github.io/pages -F build_type=workflow
```

Expected: HTTP 201. (If you get 409 Conflict, Pages is already configured — that's fine.)

- [ ] **Step 5: Watch the workflow run**

```bash
gh run watch
```

Expected: build + deploy jobs complete successfully within ~60s.

- [ ] **Step 6: Verify the live site**

Visit `https://jasontanx.github.io/` in a browser. Click through every page. Confirm all five pages render and all five images load.

If the site shows a 404 immediately after the first deploy, wait 30-60 seconds — initial Pages provisioning can lag.

---

## Task 17: Final verification against success criteria

Walk the spec's `Success Criteria` (lines 198-206 of the design doc) end-to-end.

- [ ] **Step 1: All five pages render at `https://jasontanx.github.io/`**

Visit each: `/`, `/about/`, `/career/`, `/projects/`, `/contact/`. Each loads, each shows correct content.

- [ ] **Step 2: All five images load from `/images/`**

On `/about/`, all five images (`Career_Timeline`, `Mode_of_learning`, `Mode_of_work`, `skills1`, `skills2`) display, no broken icons. View source on one to confirm path is `/images/<file>.png`.

- [ ] **Step 3: No leftover Notion links**

```bash
cd /Users/junshengtan/Desktop/personal-blog
grep -rE "notion\.site|secure\.notion-static\.com" src/ public/ || echo "OK: no Notion links in source"
```

Expected: prints `OK: no Notion links in source`.

- [ ] **Step 4: Push-to-deploy round trip works**

Edit `README.md` (e.g., add a line under "Deploy"). Commit and push:

```bash
cd /Users/junshengtan/Desktop/personal-blog
git add README.md
git commit -m "docs: trivial readme tweak (deploy round-trip test)"
git push
gh run watch
```

Expected: workflow completes, no site change visible (README isn't published) but deployment succeeded.

- [ ] **Step 5: `content.csv` is preserved**

```bash
ls -la /Users/junshengtan/Desktop/personal-blog/content.csv
git log --all --oneline -- content.csv | head -1
```

Expected: file exists at repo root, in version control.

- [ ] **Step 6: Final commit (if any verification work touched files)**

```bash
cd /Users/junshengtan/Desktop/personal-blog
git status
```

If clean, you're done. Otherwise commit any touch-ups.

---

## Done

V1 should now be live at **https://jasontanx.github.io/** with five pages, all migrated content, and automatic redeploys on push.

Future work (deferred per spec):
- `/blog` route + Wordpress post migration
- Custom domain
- Dark mode, RSS, search, comments, analytics
