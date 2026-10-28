# 3adao-blog

Markdown source of truth for the 3A DAO blog at **https://3adao.org/blog**.

Write a file in `content/`, open a pull request, and once it is merged to `main` Vercel rebuilds the site. There is
exactly one copy of every sentence: the repo. The site is a build artefact.

## Layout

```
content/*.md     the posts (file name = slug)
content/img/**   images (png, jpg, gif, webp), copied byte for byte
build.mjs        markdown -> dist/. one dependency (marked)
middleware.js    serves the markdown source to AI agents at the canonical URL
vercel.json      build config, security headers, content types
test/            node:test suite for the build (npm test)
```

## Adding a post

Create `content/<slug>.md`. The slug is the file name without `.md` and must match `^[a-z0-9]+(-[a-z0-9]+)*$`.

```markdown
---
title: The OPS swap is live
description: One sentence, used for the meta description, the feed summary and llms.txt.
date: 2026-11-01
author: 3A DAO
tags: swap, ops
---

Body in markdown.
```

| Field         | Required | Meaning                                                        |
|---------------|----------|----------------------------------------------------------------|
| `title`       | yes      | Post title                                                     |
| `description` | yes      | Meta description, feed summary, llms.txt line                  |
| `date`        | yes      | Publication date, `YYYY-MM-DD`, a real calendar date           |
| `author`      | yes      | Author name                                                    |
| `tags`        | no       | Comma-separated                                                |
| `updated`     | no       | Last update, `YYYY-MM-DD`; defaults to `date`                  |
| `draft`       | no       | `true` excludes the post from every output                     |

The build fails, naming the file and the field, if a required field is missing, `date` is not a valid date, or the
slug is invalid. Posts dated in the future are still published; there is no scheduling.

Images go in `content/img/` and are referenced as `![alt text](/blog/img/name.png)`. Alt text is required. Internal
links must start with `/blog/`. `<script>` in a post fails the build.

## Local build

```bash
npm install
npm run build      # -> dist/
npm run check      # exit 1 if dist/ is stale or missing
npm test           # node:test, no extra dependencies
```

`BLOG_CONTENT_DIR` and `BLOG_OUT_DIR` override the content and output directories (the tests use them).

## The /blog prefix

The blog is served at 3adao.org/blog through a rewrite from another Vercel project (`3adao-landing`), so every URL the build emits
lives under `/blog`: `/blog` (index), `/blog/<slug>`, `/blog/<slug>.md`, `/blog/index.md`, `/blog/feed.xml` (Atom),
`/blog/sitemap.xml`, `/blog/llms.txt`, `/blog/img/**`. Two constants at the top of `build.mjs`, `SITE_ORIGIN` and
`BASE_PATH`, drive everything; `middleware.js` and `vercel.json` repeat the prefix and must be changed with them.

Every post is also served as its markdown source, byte for byte, at `/blog/<slug>.md`, and at the canonical URL to
AI-agent user-agents or requests with `Accept: text/markdown`.

## Why almost no dependencies

`marked` is the only one. A blog is a public surface that nobody watches: a compromised build step would serve every
reader and every agent that trusts us. The build is a single file you can read in a sitting, and there is no
client-side JavaScript, so nothing needs a browser to be read.
