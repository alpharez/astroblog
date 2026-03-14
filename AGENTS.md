# Repository Guidelines

## Project Structure & Module Organization
This repository is an Astro 5 personal site. Application routes live in `src/pages/`, with the blog index at `src/pages/index.astro` and post routes handled by `src/pages/blog/[...slug].astro`. Shared UI lives in `src/components/`, page wrappers in `src/layouts/`, and site metadata in `src/consts.ts`. Blog content is stored in `src/content/blog/` as Markdown or MDX, validated by `src/content.config.ts`. Static files such as fonts, icons, and redirects belong in `public/`. Built output is generated into `dist/` and should not be edited by hand.

## Build, Test, and Development Commands
Run commands from the repository root:

- `npm install` installs dependencies.
- `npm run dev` starts the Astro dev server on `localhost:4321`.
- `npm run build` creates the production build in `dist/`.
- `npm run preview` serves the built site locally for final checks.
- `npm run astro -- check` runs Astro's project validation, including content schema checks.

## Coding Style & Naming Conventions
Follow the existing Astro and TypeScript style in each file. Current source files use tabs in `.astro` and TypeScript files; avoid reformatting unrelated code. Use `PascalCase` for component files such as `Header.astro`, camelCase for local variables, and kebab-case for blog filenames such as `enterprise-ssl-certificate-automation.md`. Keep frontmatter minimal and explicit: `title`, `description`, `pubDate`, with optional `updatedDate` and `heroImage`. No dedicated lint or format tooling is configured, so consistency is enforced by careful diffs and Astro checks.

## Testing Guidelines
There is no standalone automated test suite in this repository. Treat `npm run build` and `npm run astro -- check` as the required validation steps before opening a PR. When changing layout or styling, also verify the affected pages in `npm run dev` or `npm run preview`. For content changes, confirm the post renders correctly and that dates, images, and slug paths resolve.

## Commit & Pull Request Guidelines
Recent commits use short, lowercase, descriptive subjects such as `dep fixes` and `ai outage blog`. Keep commit messages concise and focused on one change. Pull requests should include a short summary, note any content or layout impact, and attach screenshots for visible UI changes. Link related issues when applicable and mention the validation command you ran.

## Content Authoring Notes
New posts should be added under `src/content/blog/` with a kebab-case filename. Prefer direct, technical writing with working command examples and concise headings. Store post-specific images next to the content or under `src/assets/blog/` when they are processed by Astro.
