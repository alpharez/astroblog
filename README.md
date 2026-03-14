# steveclement.me

> Personal site and technical blog for Steve Clement, built with Astro 6.

<p>
  <a href="https://steveclement.me"><img alt="Live Site" src="https://img.shields.io/badge/live-steveclement.me-8ab4ff?style=for-the-badge"></a>
  <a href="https://steveclement.me/rss.xml"><img alt="RSS Feed" src="https://img.shields.io/badge/rss-subscribe-77e4c8?style=for-the-badge"></a>
  <a href="./src/content/blog"><img alt="Blog Content" src="https://img.shields.io/badge/content-markdown%2Fmdx-a7b1bd?style=for-the-badge"></a>
  <a href="./package.json"><img alt="Astro 6" src="https://img.shields.io/badge/astro-6.0.4-c0d7ff?style=for-the-badge"></a>
</p>

The site focuses on networking, security, automation, AI, and systems writing. Content is authored in Markdown and MDX, rendered through Astro content collections, and published as a static site.

## Quick Start

```bash
npm install
npm run dev
```

Then open `http://localhost:4321`.

## Stack

- Astro 6
- `@astrojs/mdx`
- `@astrojs/rss`
- `@astrojs/sitemap`
- `sharp`

## Project Structure

```text
.
├── public/                 # Static files, fonts, favicon, redirects
├── src/
│   ├── assets/             # Astro-managed images
│   ├── components/         # Shared UI components
│   ├── content/blog/       # Blog posts in .md and .mdx
│   ├── layouts/            # Shared page layouts
│   ├── pages/              # Route entrypoints
│   ├── content.config.ts   # Content collection schema
│   └── styles/global.css   # Global site styling
├── astro.config.mjs
├── package.json
└── tsconfig.json
```

## Common Commands

| Command | Purpose |
| --- | --- |
| `npm run dev` | Start the local site at `http://localhost:4321` |
| `npm run build` | Build the production site into `dist/` |
| `npm run preview` | Serve the built output locally |
| `npm run astro -- check` | Run Astro type and content validation |

## Writing Posts

Add new posts in `src/content/blog/` using kebab-case filenames:

```text
src/content/blog/my-new-post.md
```

Required frontmatter:

```yaml
---
title: "Post Title"
description: "Short summary"
pubDate: "2026-03-14"
---
```

Optional fields:

- `updatedDate`
- `heroImage`

## Architecture Notes

- Site metadata lives in `src/consts.ts`
- Blog post rendering uses `src/layouts/BlogPost.astro`
- Collection validation lives in `src/content.config.ts`
- RSS and sitemap output are generated during build
