# Copilot Instructions for lybeckerblog

## Project Overview

This is a personal tech blog by Anders Lybecker, hosted on **GitHub Pages** at [www.lybecker.com](https://www.lybecker.com). It uses **Jekyll** with the **Minimal Mistakes** remote theme (`mmistakes/minimal-mistakes`).

## Local Development

```bash
bundle install          # install dependencies
bundle exec jekyll serve  # serve locally at http://localhost:4000 (live reload on :35729)
```

A DevContainer config is provided (`.devcontainer/`) using the `mcr.microsoft.com/devcontainers/jekyll` image.

## Repository Structure

```
_posts/          # Blog posts (Markdown)
_pages/          # Static pages (about, speaking, archives)
_data/           # Site data (navigation.yml, messages.yml)
_config.yml      # Jekyll/Minimal Mistakes configuration
assets/images/   # Images for posts and site chrome
wp-content/      # Legacy images migrated from WordPress
index.html       # Home page layout
Gemfile          # Ruby dependencies (github-pages gem)
CNAME            # Custom domain: www.lybecker.com
```

## Blog Post Conventions

### Filename

```
_posts/YYYY-MM-DD-slug-with-dashes.md
```

Use lowercase, hyphen-separated slugs derived from the title. The date prefix determines the publish date.

### Front Matter

Every post must include YAML front matter. Follow this structure:

```yaml
---
title: Human-Readable Post Title
excerpt: One-sentence summary of the post (optional but preferred)
permalink: /blog/YYYY/MM/DD/slug-with-dashes/
categories:
  - CategoryName
tags:
  - Tag1
  - Tag2
---
```

**Rules:**
- `permalink` must match the pattern `/blog/YYYY/MM/DD/slug/` — the date and slug must correspond to the filename.
- `categories` and `tags` are arrays (use the `- item` list syntax).
- Do not add `layout`, `author_profile`, `read_time`, `comments`, `share`, `related`, `show_date`, or `classes` — these are set globally via `_config.yml` defaults.
- Use `excerpt` when the first paragraph of the post doesn't make a good standalone summary.

### Content

- Write in Markdown (Kramdown dialect).
- Use fenced code blocks with language hints (e.g., ` ```csharp `, ` ```bash `).
- Place images in `assets/images/` for new posts. Reference legacy images from `wp-content/` as-is — do not move them.
- Use relative paths for internal links: `/blog/YYYY/MM/DD/slug/`.
- The reusable warning `{% include messages.yml %}` data is in `_data/messages.yml` — prefer data-driven includes over duplicating text.

## Static Pages

Pages live in `_pages/` with their own front matter including a `permalink`. They inherit the `single` layout and `wide` classes from `_config.yml` defaults.

## Formatting & Editor Settings

Defined in `.editorconfig`:
- **Charset:** UTF-8
- **Line endings:** LF
- **Indentation:** 2 spaces
- **Trailing whitespace:** trimmed
- **Final newline:** always

## Theme & Plugins

- **Theme:** Minimal Mistakes via `remote_theme: mmistakes/minimal-mistakes` — do not switch to a gem-based theme.
- **Skin:** `default`
- **Plugins** (configured in `_config.yml` and `Gemfile`): jekyll-redirect-from, jekyll-seo-tag, jekyll-sitemap, jekyll-avatar, jemoji, jekyll-mentions, jekyll-include-cache, jekyll-feed, jekyll-paginate, jekyll-gist.

Do not add plugins that are incompatible with GitHub Pages unless the build process is changed.

## Things to Avoid

- Do not modify `CNAME` or `_config.yml` site URL/baseurl without explicit instruction.
- Do not change the `remote_theme` or `minimal_mistakes_skin` without explicit instruction.
- Do not add Node.js, webpack, or other non-Jekyll build tooling.
- Do not alter permalink structure for existing posts — external links depend on them.

## Agent Readiness

After any change that affects the site's public-facing content, structure, or configuration, verify agent-readiness at <https://isitagentready.com/>. Key files that support agent discovery:

- `robots.txt` — AI crawler rules (GPTBot, ClaudeBot, PerplexityBot, etc.)
- `llms.txt` — LLM-friendly site overview following the llmstxt.org spec

If your changes affect navigation, site metadata, or content structure, update `llms.txt` accordingly.
