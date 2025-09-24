# 📘 FSSG: Comprehensive Documentation & Specification (v1.1 — Production Grade)

**FSSG (FACET Static Site Generator)** is a deliberately lightweight, deterministic static-site engine. It consumes canonical JSON emitted by the FACET language and produces production-ready artefacts without surprises, hidden state, or runtime side effects.

> **Mission** – Deliver a publishing toolchain where FACET remains the single, strictly validated source of truth that deterministically compiles into fast, secure, and SEO-optimised static sites.
>
> **Guiding principle** – `One source → One artefact. No magic. No surprises.`

## Table of Contents
1. [Immutable Core Principles](#immutable-core-principles)
2. [Architecture & Build Pipeline](#architecture--build-pipeline)
3. [Project Structure](#project-structure)
4. [Configuration (`fssg.config.json`)](#configuration-fssgconfigjson)
5. [FACET Web DSL Extensions](#facet-web-dsl-extensions)
6. [Routing & Redirects](#routing--redirects)
7. [Component System](#component-system)
8. [SEO, i18n & Metadata](#seo-i18n--metadata)
9. [Performance & Optimisation](#performance--optimisation)
10. [Security](#security)
11. [Accessibility (A11y)](#accessibility-a11y)
12. [Interactivity & Islands](#interactivity--islands)
13. [Render Targets & Adapters](#render-targets--adapters)
14. [Plugin System (Renderer & Hooks API)](#plugin-system-renderer--hooks-api)
15. [Build, CI/CD & Developer Experience](#build-cicd--developer-experience)
16. [Validation & Error Catalogue](#validation--error-catalogue)
17. [Sample `.facet` Post](#sample-facet-post)

---

## Immutable Core Principles
- **Determinism** – Identical inputs must produce byte-for-byte identical outputs.
- **Zero JS by default** – Sites render fully without JavaScript; interactive islands are opt-in.
- **No attribute interpolation** – FACET forbids interpolation inside attribute blocks (spec rule F304).
- **Single canonical domain** – Alternate hostnames must 301-redirect to the canonical origin.

---

## Architecture & Build Pipeline
1. **Parse** – Convert `.facet` sources to canonical JSON.
2. **Validate** – Enforce web contracts (metadata, routes, performance budgets).
3. **Route** – Construct the sitemap, redirects, and index bundles.
4. **Render** – Transform JSON trees into the target formats (HTML, JSX, etc.).
5. **Optimise** – Run critical CSS extraction, asset hashing, Brotli/Gzip precompression.
6. **Emit** – Persist the fully optimised site into `dist/`.

---

## Project Structure
```text
fssg-project/
├── content/
│   ├── site.facet
│   ├── pages/
│   ├── posts/
│   ├── partials/
│   └── redirects.facet
├── layouts/
├── components/
├── theme/
├── client/
├── public/
└── fssg.config.json
```

---

## Configuration (`fssg.config.json`)
```json
{
  "site": { "canonical": "https://rokoss21.tech", "langDefault": "ru", "timezone": "UTC" },
  "paths": { /* content, layouts, components, theme, public, output */ },
  "blog": { /* listings, pagination, taxonomies */ },
  "performance": {
    "assetHashing": true,
    "precompress": ["brotli", "gzip"],
    "criticalCss": true,
    "imagePipeline": { "formats": ["webp", "avif"] },
    "budget": { "css": "50kb", "js": "75kb" }
  },
  "security": {
    "cspTemplate": "default-src 'self'; script-src 'self'; ...",
    "headers": {
      "Referrer-Policy": "strict-origin-when-cross-origin"
    }
  },
  "render": {
    "targets": ["html", "islands"],
    "islands": { "runtime": "react" }
  },
  "css": {
    "strategy": "tailwind",
    "tailwind": { "config": "./tailwind.config.js", "safelistFromFacet": true }
  },
  "client": { "entry": "./client/index.ts", "inject": "auto" },
  "renderers": ["@fssg/renderer-html", "@fssg/renderer-react"],
  "hooks": ["./hooks/tailwind-safelist.ts"]
}
```

---

## FACET Web DSL Extensions
- `@page(..., status="draft|scheduled|published")` – content lifecycle flag.
- `@meta` – extended keys: `robots`, `noindex`, `alt_langs` for i18n, structured data controls.
- `@head` – optional HTTP header management (`csp`, `cache_control`).
- `@redirects` – declarative redirects compiled to hosting-native rulesets.
- Additional blocks include `@table`, `@math` (optional SSR for LaTeX), and enriched list/item metadata.

---

## Routing & Redirects
- Declarative sitemap derived from file system plus explicit overrides.
- `redirects.facet` produces platform-specific configurations (Netlify `_redirects`, `vercel.json`, etc.).
- Slug validation enforces ASCII-safe URLs and prevents reserved paths.

---

## Component System
- Components live under `components/` with strict prop typing.
- Supports accessibility-first defaults, automatic prop validation, and component-level metadata (e.g., CSP rules).
- Islands leverage component contracts but hydrate selectively.

---

## SEO, i18n & Metadata
- **Internationalisation** – `@meta.alt_langs` generates hreflang tags and locale-aware routes.
- **JSON-LD** – Automatic schema for `WebSite`, `BlogPosting`, authorship, and media metadata.
- **OpenGraph** – Full suite of OG tags, including image dimensions where available.
- **Indexing controls** – `noindex`, `nofollow`, `noarchive`, and canonical URLs all orchestrated via `@meta`.

---

## Performance & Optimisation
- **Incremental builds** – Dependency graph recalculates only affected pages.
- **Asset hashing** – Long-term caching via deterministic fingerprinted filenames.
- **Precompression** – Emit `.br` and `.gz` variants for static hosting.
- **Critical CSS** – Inline above-the-fold styles; defer the rest.
- **Image pipeline** – Optional conversion to modern formats and responsive `srcset` generation.

---

## Security
- **Strict CSP** – Deterministic Content-Security-Policy generation per route.
- **Security headers** – Automatic `Referrer-Policy`, `Permissions-Policy`, `X-Content-Type-Options`, etc.
- **HTML sanitisation** – User-supplied markup is sanitised through an allowlist.
- **Plugin sandboxing** – Plugin manifests declare explicit capabilities; no implicit host access.

---

## Accessibility (A11y)
- Native `skip to content` support and focus-visible styling.
- Default theme meets WCAG AA contrast (≥ 4.5:1).
- Dedicated print stylesheet for hard-copy use cases.

---

## Interactivity & Islands
- Opt-in islands follow the `data-behavior` contract.
- Hydration targets can be pure Web Components, React, or custom runtimes defined in `render.targets`.
- Islands include deterministic bundling and CSS scoping to avoid cross-contamination.

---

## Render Targets & Adapters
- CI/CD executes contract tests across all configured render targets (`html`, `islands`, `react`) and snapshots output.
- Optional “Shadcn Bridge” provides adapters for shadcn/ui primitives (Button, Card, Dialog) with deterministic styling.

---

## Plugin System (Renderer & Hooks API)
- Stable API surface for renderers and lifecycle hooks (pre-parse, post-render, emit).
- Plugins run in a sandbox with explicit inputs/outputs and deterministic execution order.

---

## Build, CI/CD & Developer Experience
- **Hermetic builds** – Containerised pipelines with pinned dependency versions; timestamps stored in UTC.
- **Project generator** – `fssg init --retro` scaffolds a ready-to-use retro-themed site.
- **Build reports** – Each build may emit `dist/_report.html` summarising timings, bundle sizes, and key metrics.
- **Performance budgets** – Builds fail fast if CSS/JS artefacts exceed defined thresholds.

---

## Validation & Error Catalogue

| Code  | Description                                            |
| :---- | :------------------------------------------------------|
| `H101` | Missing `@meta.title`                                  |
| `H102` | `@meta.description` exceeds 160 characters             |
| `H103` | Duplicate route detected                              |
| `H105` | Invalid ISO date (expected `YYYY-MM-DD`)               |
| `C201` | Component missing required prop `"X"`                 |
| `J102` | Behaviour `"X"` not registered                        |
| `F304` | Attribute interpolation detected (FACET spec violation)|
| `S101` | CSP violation detected in component                    |
| `P101` | Asset size exceeds performance budget                  |

---

## Sample `.facet` Post

```facet
@page(type="post", layout="default", status="published")

@meta
  title: "Deterministic Prompts"
  description: "Why determinism stabilises AI pipelines and how FACET delivers it."
  date: "2025-09-23"
  tags: ["facet", "architecture", "retro"]
  slug: "deterministic-prompts"
  robots: "index, follow"
  alt_langs:
    - { lang: "en", href: "/en/blog/deterministic-prompts" }

@vars
  site_name: "FACET Blog"
  author: "Emil Rokossovskiy"

@body
  h1: "{{site_name}}: Deterministic Prompts █"
  intro: "FACET transforms prose into canonical JSON. One source → one artefact. That is the foundation of reliable systems."
  separator: true
  content:
    section1:
      h2: "What determinism means"
      p: "Determinism is the guarantee that identical inputs yield identical outputs."
    section2:
      h2: "FACET advantages"
      benefits:
        - "Reproducible builds"
        - "Strict validation"
        - "Canonical JSON"
        - "Lens-powered transformations"
  cta:
    text: "Explore FACET"
    href: "/facet"
    class: "btn-primary"
  footer:
    author: "Author: {{author}}"
    date: "23 September 2025"
```

---

**FSSG keeps the build graph explicit, the artefacts hermetic, and the workflow transparent – enabling teams to reason about every byte that reaches production.**
