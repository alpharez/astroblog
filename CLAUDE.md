# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is steveclement.me, a personal blog built with Astro 5. The site publishes technical blog posts about network automation, security, and related topics.

## Commands

```bash
npm run dev      # Start dev server at localhost:4321
npm run build    # Build production site to ./dist/
npm run preview  # Preview production build locally
```

## Architecture

**Content System**: Blog posts live in `src/content/blog/` as Markdown/MDX files. The schema in `src/content.config.ts` requires frontmatter with `title`, `description`, `pubDate`, and optional `updatedDate` and `heroImage`.

**Routing**: File-based routing in `src/pages/`. The blog index is at `/blog/` and individual posts use the dynamic route `src/pages/blog/[...slug].astro`.

**Layout**: Single layout `src/layouts/BlogPost.astro` wraps all blog posts. Shared components (Header, Footer, BaseHead) are in `src/components/`.

**Configuration**: Site metadata lives in `src/consts.ts`. Astro config includes MDX and sitemap integrations.

## Adding Blog Posts

Create a new `.md` or `.mdx` file in `src/content/blog/` with this frontmatter:

```yaml
---
title: "Post Title"
description: "Brief description"
pubDate: "2026-01-17"
heroImage: "./optional-image.jpg"  # Optional, relative to the post
---
```

Images referenced in `heroImage` should be placed alongside the post in `src/content/blog/`.
