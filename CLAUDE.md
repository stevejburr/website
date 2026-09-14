# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Steve Burr's personal blog (stevejburr.com — "Data, Visualisation, Insights"), built with
[blogdown](https://bookdown.org/yihui/blogdown/) (R Markdown) on top of [Hugo](https://gohugo.io/),
using the `minimal` Hugo theme. Posts are data-analysis writeups (sport analytics, stats/Bayesian
modeling, dataviz challenges like #TidyTuesday and #MakeoverMonday) authored as R Markdown, knit to
static HTML/Markdown, and rendered by Hugo.

## Build / preview commands

There is no npm/node toolchain — everything runs through R + the `blogdown` package inside RStudio
(`20180827-2.Rproj`, project `BuildType: Website`). From an R console in the repo root:

```r
blogdown::serve_site()      # live-preview server, rebuilds on save
blogdown::build_site()      # full site build -> public/ (gitignored, not committed)
blogdown::new_post(title = "...", ...)   # scaffold a new post under content/post/
```

Hugo itself can also be invoked directly (`hugo server`, `hugo`) if installed, since blogdown is a
thin wrapper around it — but posts must be *knit* via blogdown/knitr first, plain `hugo` won't render
the `.Rmd` source.

There is no test suite, linter, or CI config in this repo. There is no `renv.lock`/package manifest —
R package dependencies (tidyverse, ggplot2, ggrepel, etc., per-post) are whatever's installed locally;
check the `library(...)` calls at the top of a post's `.Rmd` for what it needs.

`public/` is the Hugo build output and is gitignored — never hand-edit files there, they're
regenerated. `resources/_gen` is Hugo's asset cache, also generated.

## Content structure

- `content/post/*.Rmd` — source for most posts (R Markdown, executable code chunks). Knitting
  produces a sibling `.html` (or `.markdown`) fragment in the same directory plus a
  `<slug>_files/figure-html/*.png` folder for generated plots — both the source and the knitted
  output are committed to git (this is the standard blogdown pattern: Hugo builds from the knitted
  Markdown/HTML, not from R directly).
- `content/post/*.md` / `*.markdown` — a few older posts are plain Markdown with no R component.
- Post front matter convention (YAML):
  ```yaml
  ---
  title: Post Title
  author: Steve
  date: 'YYYY-MM-DD'
  slug: post-slug
  categories: [Sports, R, Statistics, ...]
  tags: []
  ---
  ```
- `content/Top-Posts.Rmd` — a curated "best of" listing page (not under `post/`), knits to
  `content/Top-Posts.html`.
- Per-post data/asset files (CSVs, `.rds`, images used as inputs) live alongside the post's `.Rmd`
  in `content/post/` rather than in a separate data directory.

## Site/theme structure

- `config.toml` — Hugo site config: menu items, social icons (`[[menu.icon]]`, name must match a
  Font Awesome icon), theme params (accent color, font, syntax highlighting languages).
- `themes/minimal/` — the vendored theme (plain copy in-tree, **not** a git submodule despite the
  theme's own README recommending one — don't assume `git submodule` commands will do anything here).
- `layouts/partials/list-item.html` — a project-level override of the theme's post-listing partial
  (controls how each post renders in list/index views: title link, date/description subtitle, repo
  link resolution, tag badges). Hugo resolves `layouts/` before `themes/<name>/layouts/`, so edit
  overrides here rather than inside `themes/minimal/`.
- `static/` — static assets copied verbatim into the build.
