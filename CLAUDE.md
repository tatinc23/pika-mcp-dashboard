# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file (`index.html`) static reference dashboard for Pika's MCP. No build step, no dependencies, no package.json. Deployed to GitHub Pages automatically on push to `main`.

## Local dev

```bash
python3 -m http.server 8080
# Open http://localhost:8080
```

## Architecture

Everything lives in `index.html`:

- **CSS** — top of `<style>` block. CSS custom properties for theming (dark/light via `data-theme` on `<html>`). Design system: Syne + Space Mono fonts, electric lime (`#c8ff00`) + hot pink (`#ff2cf4`) accent palette.
- **TOOLS array** — the only data source. Each object is either a `badge:'tool'` or `badge:'skill'`. All rendering is derived from this array — never add HTML cards manually.
- **CAT_ORDER / CAT_META** — define category display order and metadata. Skills always render first as large cards (`skills-strip`); tools render as compact grid cards (`cards-grid`).
- **renderGrid()** — builds the entire tool grid from TOOLS + CAT_META.
- **openModal(id)** — finds tool by id, builds and shows the modal form, calls `cmdFn` to generate the copy-pasteable command.
- **Search** — full-text filter across name + desc + cmd, triggered by ⌘K.
- **Tabs** — Tools tab vs Docs tab. Docs tab is static HTML below the script block.

## Adding a skill (the common task)

Find the `TOOLS` array and add before the `// GENERATE VIDEO` comment:

```js
{
  id: 'skill_yourskill',
  name: 'Your Skill Name',
  cat: 'skills',
  badge: 'skill',
  icon: '🎯',
  cmd: '/pika:your-skill',
  desc: 'One-line card description.',
  explainer: '<strong>Lead with what the user provides.</strong> Explain what Pika auto-generates.',
  params: [{ name: 'input', label: 'Input', type: 'textarea', req: true, hint: 'hint text' }],
  optionalParams: [
    { name: 'aspect_ratio', label: 'Aspect Ratio', type: 'select', options: ['16:9','9:16','1:1'] }
  ],
  attachNote: 'Optional image attachment guidance.',
  cmdFn: function(a) {
    return '/pika:your-skill input="' + (a.input || '') + '"';
  }
}
```

The `skills-strip` grid is `repeat(3, 1fr)` — adding a 4th skill wraps to a new row naturally. No layout changes needed.

## Current skills (Pika plugin v1.2.3 + GitHub latest)

Installed (v1.2.3): `podcast`, `explainer`, `ugc-ads`, `baseball-trend`, `kiss-cam`

On GitHub (Pika-Labs/Pika-Plugins): + `app-sizzle`, `app-store-screens`, `build-a-brand`, `founder-product-video`

## Deploy

Push to `main` → GitHub Pages (`tatinc23.github.io/pika-mcp-dashboard`) auto-deploys. No CI config needed — it's a plain HTML file served directly.
