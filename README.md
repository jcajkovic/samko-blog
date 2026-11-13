# Samkové umelecké diela (`samko-blog`)

Personal art portfolio and blog showcasing artworks created by **Samuel (Samko)**, built with [Hugo](https://gohugo.io/) and the [Newsroom](https://github.com/onweru/newsroom) theme, and automatically deployed to GitHub Pages.

- **Live Website:** [https://jcajkovic.github.io/samko-blog/](https://jcajkovic.github.io/samko-blog/)
- **Repository:** [https://github.com/jcajkovic/samko-blog](https://github.com/jcajkovic/samko-blog)

---

## Table of Contents

- [Overview & Features](#overview--features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Getting Started (Local Development)](#getting-started-local-development)
- [Content Management](#content-management)
  - [Adding a New Artwork Post](#adding-a-new-artwork-post)
  - [Post Front Matter Reference](#post-front-matter-reference)
  - [Editing the About Page](#editing-the-about-page)
  - [Custom Shortcodes](#custom-shortcodes)
- [Image & Media Hosting](#image--media-hosting)
- [Site Configuration & Customization](#site-configuration--customization)
  - [Main Configuration (`config.toml`)](#main-configuration-configtoml)
  - [Navigation Menu (`data/menu.yml`)](#navigation-menu-datamenuyml)
  - [Footer Attribution (`data/designer.yml`)](#footer-attribution-datadesigneryml)
  - [Overriding Styles & Layouts](#overriding-styles--layouts)
- [Deployment & CI/CD](#deployment--cicd)
  - [GitHub Pages (Active)](#github-pages-active)
  - [AWS S3 Deployment (Optional)](#aws-s3-deployment-optional)
- [Troubleshooting](#troubleshooting)
- [License & Credits](#license--credits)

---

## Overview & Features

**Samkové umelecké diela** (*Samko's Artworks*) is a static portfolio site written in Slovak where Samuel's paintings, sketches, and creative studio works are published with high-resolution imagery, descriptions, dates, and tags.

Key features provided by the setup and theme:

- **Minimalist, Responsive Gallery/Blog Layout:** Inspired by Apple's Newsroom, built with CSS Grid, Flexbox, and vanilla JavaScript (zero jQuery or heavy frontend dependencies).
- **Light & Dark Mode:** Defaults to light mode (`defaultMode = "light"`) while providing a persistent UI toggle in the navigation menu so visitors can switch themes at any time.
- **Taxonomy Support:** Automatic tag (`/tags/`) and category (`/categories/`) listing pages and RSS feeds (`index.xml`).
- **SEO & Social Sharing:** Built-in OpenGraph tags, Twitter Card metadata, Schema.org `BlogPosting` JSON-LD structured data, `sitemap.xml`, and `robots.txt` generation.
- **Heading Deeplinks:** Hovering over Markdown headings reveals a copy-link button for sharing direct section anchors.
- **Native Lazy Loading:** Deferred loading for images and iframes out of the box.

---

## Tech Stack

| Layer | Technology | Details |
| :--- | :--- | :--- |
| **Static Site Generator** | [Hugo Extended](https://gohugo.io/) | Minimum `v0.63.0`; CI builds with `v0.111.3` (`hugo_extended`) |
| **Stylesheet Compiler** | Sass (`dart-sass-embedded` / LibSass) | Required by the `newsroom` theme's `.sass` pipeline |
| **Theme** | [onweru/newsroom](https://github.com/onweru/newsroom) | Tracked as a Git submodule under `themes/newsroom` |
| **Content Format** | Markdown (`.md`) + YAML/TOML Front Matter | Rendered via Hugo's Goldmark engine |
| **Media Storage** | Local `static/images/` & AWS S3 | Artwork photos hosted in S3 (`eu-west-1`) or bundled locally |
| **CI/CD & Hosting** | GitHub Actions + GitHub Pages | Automated build & deploy via `.github/workflows/hugo.yml` |

---

## Project Structure

```text
samko-blog/
├── .github/
│   └── workflows/
│       └── hugo.yml          # GitHub Actions workflow for building & deploying to GitHub Pages
├── archetypes/
│   └── default.md            # Default front matter template used by `hugo new`
├── assets/                   # Custom Hugo Pipes assets (override theme Sass/JS here)
├── content/
│   ├── about.md              # "O mne" (About Me) page (/about/)
│   └── post/                 # Artwork entries / blog posts
│       └── labut.md          # Example artwork post ("Labuť")
├── data/
│   ├── designer.yml          # Theme designer attribution rendered in the footer
│   └── menu.yml              # Top navigation bar links
├── layouts/                  # Custom Hugo HTML template overrides (overrides themes/newsroom/layouts)
├── public/                   # Generated static site output (rebuilt cleanly in CI)
├── resources/                # Cached Hugo asset pipeline artifacts (compiled Sass)
├── static/
│   └── images/               # Static images copied directly to `/images/`
│       ├── samko-sketch-small.png
│       ├── samko-sketch-square.png
│       └── samko-sketch.png
├── themes/
│   └── newsroom/             # Git submodule -> https://github.com/onweru/newsroom.git
├── .gitignore                # Git ignore rules
├── .gitmodules               # Git submodule definition for `themes/newsroom`
├── config.toml               # Main Hugo site configuration
├── go.mod                    # Go module definition (Go 1.20, references onweru/newsroom)
└── go.sum                    # Go module checksums
```

---

## Prerequisites

Before running or building the site locally, ensure you have the following installed:

1. **Git** — required to clone the repository and initialize the `themes/newsroom` submodule.
2. **Hugo Extended** (`>= 0.63.0`, recommended `>= 0.111.3`) — **must be the Extended edition** so Hugo can compile the theme's Sass stylesheets.
   - **macOS (Homebrew):**
     ```bash
     brew install hugo
     ```
     *(Homebrew installs the Extended edition by default.)*
   - **Ubuntu / Debian:**
     Download `hugo_extended_<version>_linux-amd64.deb` from [Hugo Releases](https://github.com/gohugoio/hugo/releases).
   - Verify Extended support:
     ```bash
     hugo version
     # Look for "+extended" in the version output
     ```
3. *(Optional)* **Dart Sass Embedded** (`dart-sass-embedded` / `sass`) if your local Hugo setup uses Dart Sass, and **Go `>= 1.20`** only if you switch from the Git submodule to Hugo Modules.

---

## Getting Started (Local Development)

### 1. Clone the Repository (with Submodules)

Because `themes/newsroom` is configured as a Git submodule in `.gitmodules`, clone with `--recurse-submodules`:

```bash
git clone --recurse-submodules git@github.com:jcajkovic/samko-blog.git
cd samko-blog
```

If you have already cloned the repository without submodules, initialize and fetch the theme submodule manually:

```bash
git submodule update --init --recursive
```

### 2. Start the Local Development Server

Run Hugo's built-in live-reloading server:

```bash
# Serve published content only
hugo server

# Serve including draft posts (`draft: true`)
hugo server -D
```

Open [http://localhost:1313/](http://localhost:1313/) in your browser. Any changes to `content/`, `data/`, `static/`, or `config.toml` will hot-reload automatically.

### 3. Build for Production Locally

To verify a clean production build with minification and cache garbage collection (matching CI):

```bash
hugo --gc --minify
```

Generated static files are written to the `public/` directory.

---

## Content Management

### Adding a New Artwork Post

All artwork entries live inside `content/post/`.

#### Option A: Using the Hugo CLI

```bash
hugo new post/nazov-diela.md
```

This creates `content/post/nazov-diela.md` from `archetypes/default.md` (which sets `draft: true` by default). Update the front matter fields and **remove `draft: true` (or set `draft: false`)** when ready to publish.

#### Option B: Creating the Markdown File Manually

Create a new `.md` file directly in `content/post/` (for example, `content/post/labut.md`):

```markdown
---
author: Samuel
date: 2023-03-21
title: Labuť
description: "Maľba labute z ateliéru"
tags: ["bublinka", "malba"]
image: "https://samko-portfolio-311008325132.s3.eu-west-1.amazonaws.com/labut.jpg"
---

> ***Maľba labute vytvorená na tvorivom ateliéri Bublinka.***
```

### Post Front Matter Reference

Posts support YAML (`---`) or TOML (`+++`) front matter:

| Field | Type | Required | Description |
| :--- | :--- | :---: | :--- |
| `title` | `string` | Yes | Title of the artwork displayed on cards, post header, and `<title>` tag. |
| `date` | `string` | Yes | Creation or publication date (`YYYY-MM-DD` or ISO-8601 timestamp). Controls chronological ordering. |
| `author` | `string` | Recommended | Creator name (e.g., `Samuel`). Populates Schema.org `BlogPosting` author metadata. |
| `description` | `string` | Optional | Short description of the artwork. |
| `image` | `string` | Recommended | Featured artwork image. Accepts either a full `https://...` URL (e.g., S3) or a filename inside `static/images/` (e.g., `"my-painting.jpg"`). Falls back to `thumbnail.svg` on the home page if omitted. |
| `tags` | `string[]` | Optional | List of tags (e.g., `["bublinka", "malba"]`). Generates tag archive pages under `/tags/<tag>/`. |
| `categories` | `string[]` | Optional | List of categories. Generates category archive pages under `/categories/<category>/`. |
| `draft` | `boolean` | Optional | If `true`, Hugo excludes the post from production builds unless `--buildDrafts` / `-D` is passed. |

### Editing the About Page

The "O mne" page is located at `content/about.md` and uses TOML (`+++`) front matter:

```toml
+++
title = "O mne"
date = "2016-03-21"
image = 'samko-sketch-square.png'
+++

> Čaute, moje meno je Samko ! Som mladý začínajúci umelec.
```

Here `image = 'samko-sketch-square.png'` resolves automatically to `static/images/samko-sketch-square.png` (`/images/samko-sketch-square.png`).

### Custom Shortcodes

The `newsroom` theme includes two custom Hugo shortcodes you can use inside any Markdown content file:

1. **YouTube Video (`video`):**
   Embeds a responsive, styled YouTube player using the video ID:
   ```go-html-template
   {{< video "youtubeVideoID" >}}
   ```

2. **Light/Dark Mode Picture (`picture`):**
   Displays different images from `static/images/` depending on whether the visitor is in light mode or dark mode:
   ```go-html-template
   {{< picture "lightModeImage.png" "darkModeImage.png" "Image alt text" >}}
   ```

---

## Image & Media Hosting

The theme's layout templates (`single.html`, `index.html`, and `opengraph.html`) support two ways of referencing images in the `image` front matter field:

1. **External HTTPS URLs (AWS S3 — Recommended for High-Resolution Artworks):**
   - If the `image` string contains `https://`, the URL is used verbatim.
   - Artwork photographs are hosted in the public S3 bucket:
     ```text
     https://samko-portfolio-311008325132.s3.eu-west-1.amazonaws.com/<filename>
     ```
   - Storing large photos in S3 keeps the Git repository lightweight and fast to clone.

2. **Local Static Assets (`static/images/`):**
   - If the `image` value does **not** contain `https://`, the theme automatically resolves it relative to `/images/<filename>` (backed by `static/images/<filename>`).
   - Best suited for site branding, avatars, and sketches (such as `samko-sketch.png`, `samko-sketch-square.png`, and `samko-sketch-small.png`).

---

## Site Configuration & Customization

### Main Configuration (`config.toml`)

Core site settings are defined in `config.toml`:

- **Top-level settings:**
  - `baseURL` — Default base URL (`"http://example.org/"` locally; overridden dynamically during GitHub Pages CI builds via `--baseURL`).
  - `languageCode` — Language tag (`"en-us"`).
  - `title` — Site title (`"Samkové umelecké diela"`).
  - `theme` — Active theme (`"newsroom"`).
  - `enableRobotsTXT` — Generates `robots.txt` (`true`).
  - `disqusShortname` *(optional)* — Uncomment/add to enable Disqus comments on posts.
- **`[params]` section:**
  - `email` — Contact email (`"samko.cajkovic@gmail.com"`).
  - `location` — Author location (`"Vinicne, Slovensko"`).
  - `defaultMode` — Initial color scheme (`"light"` or `"dark"`). Visitors can still toggle modes manually in the UI.
  - `logo` — Filename in `static/images/` or `https://` URL for the header logo. When `""` (empty), the site `title` text is displayed instead.
  - `faviconsDir` — Path to favicon assets (`"images/icons/"`, with trailing slash).
  - `blogDir` — Main post content directory (`"post"`), used to emit `BlogPosting` structured data and Twitter cards.
  - Optional commented parameters: `ga_analytics`, `ga_verify`, `twitter`, `locale`, `mainSections`.
- **`[markup]` section:**
  - Configures Goldmark Markdown rendering (`unsafe = false` disables raw inline HTML by default; set `unsafe = true` if raw HTML is needed in Markdown) and Chroma syntax highlighting.

### Navigation Menu (`data/menu.yml`)

Top navigation links are configured in `data/menu.yml`:

```yaml
- item: O mne
  url: about/
- item: Výtvory
  url: ""
# - item: Kategorie
#   url: categories
- item: Tagy
  url: tags
```

- `url: ""` links to the site root (the main artworks feed).
- Uncomment the `Kategorie` entry if you start categorizing posts with `categories` front matter.

### Footer Attribution (`data/designer.yml`)

Controls the `"Designed by ..."` credit in the site footer (`themes/newsroom/layouts/partials/footer.html`).

### Overriding Styles & Layouts

Avoid editing files directly inside `themes/newsroom/`, as it is an external Git submodule. Instead, leverage Hugo's lookup order:

- **Custom CSS / Sass:** Copy `themes/newsroom/assets/sass/_custom.sass` to `assets/sass/_custom.sass` in the project root and add your custom rules there. Hugo will automatically merge it when compiling `main.sass`.
- **Custom HTML Templates:** Place matching template paths inside the root `layouts/` directory (for example, `layouts/partials/footer.html` overrides `themes/newsroom/layouts/partials/footer.html`).

---

## Deployment & CI/CD

### GitHub Pages (Active)

Deployment is fully automated via GitHub Actions in `.github/workflows/hugo.yml`.

- **Triggers:**
  - Every `push` to the `main` branch.
  - Manual execution from the GitHub Actions tab (`workflow_dispatch`).
- **Pipeline Architecture:**
  1. **`build` job (`ubuntu-latest`):**
     - Installs **Hugo Extended `0.111.3`** and **`dart-sass-embedded`**.
     - Checks out the repository with `submodules: recursive` and full history (`fetch-depth: 0`).
     - Configures GitHub Pages metadata (`actions/configure-pages@v3`).
     - Builds the site in production mode:
       ```bash
       hugo --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
       ```
     - Uploads `./public` as a GitHub Pages artifact (`actions/upload-pages-artifact@v1`).
  2. **`deploy` job (`ubuntu-latest`):**
     - Publishes the artifact to the `github-pages` environment (`actions/deploy-pages@v2`) at [https://jcajkovic.github.io/samko-blog/](https://jcajkovic.github.io/samko-blog/).
- **Concurrency:** Uses concurrency group `"pages"` with `cancel-in-progress: false` so active production deployments always finish cleanly while intermediate queued runs are skipped.

### AWS S3 Deployment (Optional)

`config.toml` includes a commented `[deployment]` template for Hugo's built-in `hugo deploy` command targeting AWS S3 (`s3://samko-portfolio-311008325132?region=eu-west-1`). To deploy directly to S3 instead of (or in addition to) GitHub Pages, uncomment the `[deployment]` section in `config.toml`, configure AWS credentials locally or in CI, and run `hugo && hugo deploy`.

---

## Troubleshooting

- **Blank site or `WARN Found no layout file for "HTML"` when running `hugo server`:**
  The `themes/newsroom` submodule is empty. Initialize it with:
  ```bash
  git submodule update --init --recursive
  ```
- **`TOCSS: failed to transform "sass/main.sass"` error:**
  You are running the standard (non-extended) Hugo binary. Install **Hugo Extended** (`hugo version` must include `+extended`).
- **New post does not appear on the home page or in production:**
  1. Check that the file is inside `content/post/` (matching `blogDir = "post"`).
  2. Ensure `draft: true` is removed or changed to `draft: false`.
  3. Ensure the `date` field is not set in the future (or pass `--buildFuture` if intentional).
- **Broken artwork image:**
  - For S3 images, verify the URL starts with `https://` and that the S3 object has public read permissions.
  - For local images, place the file in `static/images/<name>` and set `image: "<name>"` (do **not** prefix with `/images/` or `static/images/`, as the theme prepends `images/` automatically).

---

## License & Credits

- **Artwork & Content:** © Samuel Čajkovič (*Samkové umelecké diela*). All rights reserved.
- **Theme:** [Newsroom](https://github.com/onweru/newsroom) by [Dan Weru](https://github.com/onweru), licensed under the [MIT License](themes/newsroom/LICENSE).
