# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal website (portfolio, CV, photo gallery) built with Jekyll and hosted on GitHub Pages at https://tianyilim.github.io. There is no test suite or linter; GitHub Pages builds and deploys automatically on push to `main`.

## Commands

```sh
bundle install                 # install gems (github-pages, minima, jekyll-feed)
bundle exec jekyll serve       # local preview at http://localhost:4000, auto-rebuilds on change
bundle exec jekyll build       # build into _site/ (useful to check for Liquid/build errors)
```

`_config.yml` is not reloaded by `jekyll serve`; restart the server after editing it.

## Architecture

- **Theme:** `minima` (~2.5) via the `github-pages` gem, so only plugins whitelisted by GitHub Pages work. Theme files are overridden locally: `_layouts/` (default, home, page, post), `_includes/` (head, header, footer), and `_sass/minima*` (a vendored copy of minima's Sass). Custom CSS lives in `assets/main.scss`, which imports `minima` and adds the image grid / caption classes (`.row`, `.column`, `p.column_2`, `p.column_3`, `.row_img`, `.left_capt`/`.center_capt`/`.right_capt`).
- **Top-level pages:** `index.md` (layout `home`, which lists all posts below the About content), `portfolio.md`, `cv.md` (embeds `assets/TianyiLim_CV.pdf`), `photog.md` (permalink `/photos/`). The nav bar order is set by `header_pages` in `_config.yml`.
- **Portfolio projects are posts.** Each project is a file in `_posts/` with front matter `layout: post`, `categories: Portfolio`, `permalink: /portfolio/<slug>`, `comments: true`. `portfolio.md` is a hand-maintained index: each entry links to the post by its bare slug (e.g. `[...](forzaETH)`), which resolves relative to `/portfolio/`. Adding a project means creating the post **and** adding an entry to `portfolio.md`; the link slug must match the post's `permalink`.
- **Assets:** images/PDFs for each project go in `assets/<ProjectName>/`; gallery photos in `assets/gallery/`. Pages/posts reference them with relative paths like `../assets/...` and typically embed images as raw HTML (`<p align="left"><img width="400" src="..."></p>`) rather than Markdown image syntax, with an italic Markdown line underneath as the caption.
- **Comments:** Disqus (`disqus.shortname: tyl-website` in `_config.yml`), rendered by minima's built-in `disqus_comments.html` include from `_layouts/post.html`. It only renders when `JEKYLL_ENV=production` (as on GitHub Pages), so comments don't show up in local `jekyll serve`; set `comments: false` in a post to disable them.
