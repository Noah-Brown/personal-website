# Personal Website

My personal website and blog, built with [Astro](https://astro.build) and [Tailwind CSS](https://tailwindcss.com).

## Tech Stack

- **Framework:** Astro v5
- **Styling:** Tailwind CSS v4 (dark mode only)
- **Content:** Markdown via Astro Content Collections
- **Deployment:** GitHub Pages via GitHub Actions

## Getting Started

```bash
npm install
npm run dev
```

## Commands

| Command           | Action                                 |
| :---------------- | :------------------------------------- |
| `npm install`     | Install dependencies                   |
| `npm run dev`     | Start local dev server at `localhost:4321` |
| `npm run build`   | Build production site to `./dist/`     |
| `npm run preview` | Preview the build locally              |

## Adding Blog Posts

Create a new `.md` file in `src/content/blog/` with the following frontmatter:

```md
---
title: "Post Title"
description: "A short description."
date: 2026-02-06
tags: ["example"]
---

Your content here.
```

## Deployment

Pushing to `main` triggers the GitHub Actions workflow to deploy to GitHub Pages automatically.
