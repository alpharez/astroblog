# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is steveclement.me, a personal blog built with Astro 5. The site publishes technical blog posts about network automation, security, AI, and related topics.

## Commands

```bash
npm run dev      # Start dev server at localhost:4321
npm run build    # Build production site to ./dist/
npm run preview  # Preview production build locally
```

## Architecture

**Content System**: Blog posts live in `src/content/blog/` as Markdown/MDX files. The schema in `src/content.config.ts` requires frontmatter with `title`, `description`, `pubDate`, and optional `updatedDate` and `heroImage`.

**Routing**: The home page (`/`) shows the blog list. Individual posts use the dynamic route `src/pages/blog/[...slug].astro`.

**Layout**: Single layout `src/layouts/BlogPost.astro` wraps all blog posts. Shared components (Header, Footer, BaseHead) are in `src/components/`.

**Configuration**: Site metadata lives in `src/consts.ts`. Astro config includes MDX and sitemap integrations.

## Blog Writer Agent

When asked to write a blog post, follow these steps:

1. **Create the file**: `src/content/blog/[slug].md` using kebab-case for the filename

2. **Frontmatter format**:
```yaml
---
title: "Post Title"
description: "A compelling 1-2 sentence summary for SEO and previews"
pubDate: "YYYY-MM-DD"
---
```

3. **Writing style**:
   - Write for a technical audience (network engineers, security professionals, developers)
   - Be direct and concise - no fluff or filler
   - Use code blocks with language hints for examples
   - Prefer practical examples over abstract explanations
   - Include command-line examples where relevant

4. **Structure**:
   - Start with the problem or context (1-2 paragraphs)
   - Present the solution or main content
   - Include working code/config examples
   - End with key takeaways or next steps if appropriate

5. **Topics in scope**: Technology, security, AI, automation, networking, Linux, terminal workflows, programming (C/C++, Python, Rust, Go)
