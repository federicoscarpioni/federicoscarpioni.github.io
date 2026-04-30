# personal-blog

Source for [federicoscarpioni.github.io](https://federicoscarpioni.github.io/).
Hugo site using the [Blowfish](https://blowfish.page/) theme.
Auto-deploys to GitHub Pages on every push to `main` (see `.github/workflows/gh-pages.yaml`).

---

## Day-to-day: adding a post

Everything lives in one section: `articles`. Hugo has an archetype wired up so `hugo new` creates the right front matter automatically.

```bash
hugo new articles/eis-under-load-part-1.md
```

Open the file Hugo just created, write the body, tweak the `tags:` list, set `draft: false` when ready, then:

```bash
hugo server --buildDrafts   # preview (shows drafts)
hugo server                 # preview (published only)
git add -A && git commit -m "post: eis under load, part 1" && git push
```

### Slugs and filenames

Use lowercase-with-hyphens for filenames: `content/articles/ac-coupling-tricks.md` becomes `/articles/ac-coupling-tricks/` on the live site.

### Tags

Tags are how the site organises posts (since there's only one section). Keep the vocabulary small and consistent — it's fine to introduce a new tag, but don't coin synonyms (pick `batteries` or `battery`, not both). Two flavours of tag:

- **Topic tags**: `batteries`, `impedance`, `eis`, `control-theory`, `python`.
- **Type tags**: `methodology`, `paper-notes`, `experiment`, `tool-pick`, `book-notes`, `meta`.

Tag pages live at `/tags/<name>/` automatically (e.g. `/tags/methodology/`). The global tag index is at `/tags/`. The "Browse by topic" block in `content/articles/_index.md` links to a few featured ones — update that list as your tag vocabulary settles.

### Curated reading list (the "hub")

The Articles index page (`content/articles/_index.md`) has a "Start here" block at the top that's a hand-picked reading list — the posts you'd point someone at first. To promote a post to that list, just add a link to it under "Start here" with a one-line explanation. There's no special tag or magic; it's curated by hand on purpose.

---

## Updating the CV

The CV exists twice on purpose — a rendered page (indexable by search, readable on mobile) and a PDF (what recruiters actually want).

**The rendered page:** `content/cv/index.md`. It's a normal markdown file with headings; just edit it and commit.

**The PDF:** drop a file named `federico-scarpioni-cv.pdf` into `static/cv/`. The "Download PDF" button on the CV page links to `/cv/federico-scarpioni-cv.pdf`, which Hugo serves directly from `static/`. Replace the file and push; the URL stays the same.

```bash
cp ~/Documents/cv.pdf static/cv/federico-scarpioni-cv.pdf
git add static/cv/federico-scarpioni-cv.pdf
git commit -m "cv: update PDF"
git push
```

When you update either, bump the "Last updated" line in `content/cv/index.md` so the page and PDF don't drift.

---

## Changing the look

All site-wide settings live in **`hugo.toml`** in the repo root. The file is commented; the things you're most likely to want to change:

- **Theme colour palette**: `colorScheme` under `[params]`. Options: `blowfish`, `avocado`, `fire`, `ocean`, `forest`, `princess`, `neon`, `bloody`, `terminal`, `marvel`, `noir`, `autumn`, `congo`, `slate`.
- **Light/dark behaviour**: `defaultAppearance` (`light`, `dark`, or `auto`) and `autoSwitchAppearance`.
- **Homepage layout**: `[params.homepage] layout`. Currently `profile`. Alternatives: `page`, `background`, `hero`, `card`, `custom`.
- **Navigation**: the `[[menu.main]]` blocks at the bottom of `hugo.toml`.
- **Bio / socials**: `[params.author]`.
- **Avatar**: drop an image at `assets/img/avatar.jpg` and set `image = "img/avatar.jpg"` under `[params.author]`.

Full Blowfish reference: <https://blowfish.page/docs/configuration/>.

---

## Updating the theme

Blowfish is vendored as a git submodule. To pull new upstream changes:

```bash
git submodule update --remote themes/blowfish
git add themes/blowfish
git commit -m "chore: bump Blowfish"
git push
```

Read the [Blowfish release notes](https://github.com/nunocoracao/blowfish/releases) first — occasionally a bump requires a tiny config tweak.

If Blowfish bumps its minimum Hugo version, you'll see "Module 'blowfish' is not compatible with this Hugo version" in your next CI run. The fix is to bump `HUGO_VERSION` in `.github/workflows/gh-pages.yaml` so it matches what you're using locally (`hugo version`).

---

## Deployment

`.github/workflows/gh-pages.yaml` runs on every push to `main`: it installs Hugo, runs `hugo --gc --minify`, and publishes the `public/` output to GitHub Pages. The site URL is pinned in `hugo.toml` (`baseURL`).

If a build fails, check the "Actions" tab on GitHub — the error is almost always a broken config line or an unclosed shortcode in a new post.

---

## Troubleshooting

**The TOC sidebar isn't showing up.** Blowfish only renders the sidebar TOC on screens wide enough for it (roughly ≥1280px). On narrower screens the TOC collapses into the top of the article. If you don't see it on a wide screen, check that the post has at least two headings at level 2 or below — a single H2 isn't enough to build a TOC.

**A post isn't appearing on the homepage.** The homepage lists posts from `mainSections` only (`articles`). If you put a markdown file directly in `content/` instead of `content/articles/`, it won't show up. Also: `draft: true` posts are skipped unless you run `hugo server --buildDrafts`.

**"Error: module blowfish not found".** The submodule isn't initialised. From the repo root: `git submodule update --init --recursive`.

**Port 1313 already in use.** `hugo server -p 1314` to use a different port.
