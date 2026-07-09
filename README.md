# GWU/USEC Lab website

Source for <https://gwusec.seas.gwu.edu>. Built with [Jekyll](https://jekyllrb.com/) and published
automatically by GitHub Actions on every push to `main`.

**You should not need to write any HTML to update this site.** The content lives in YAML and
Markdown files; the HTML is generated. This README covers the things people actually change.

---

## Common tasks

### Add someone to the lab

1. Drop a square-ish photo in `imgs/people/`. Name it `NNN_firstname.jpg`, where `NNN` is the next
   number in sequence — the prefix only exists to keep the folder sorted, nothing reads it. Roughly
   400×400 is plenty; it gets displayed at 200×200 and center-cropped.
2. Add an entry to the `current:` list in [`_data/people.yml`](_data/people.yml):

   ```yaml
   - name: Ada Lovelace
     photo: 035_ada.jpg
     url: https://example.com     # optional; omit for no link
     title: PhD Student
     note: Co-Mentored with Someone Else   # optional; shown in italics
   ```

Order in the file is the order on the page. Members flow three-per-row automatically — you never
need to rebalance rows.

### Move someone to alumni

Cut their block out of `current:` and add a line to `alumni:`. Alumni entries take `degree:` and
`note:` instead of `photo:`/`title:`/`url:`:

```yaml
- name: Ada Lovelace
  degree: PhD
  note: Now at Analytical Engines Inc.   # optional; rendered in (parentheses, italics)
```

Their photo can stay in `imgs/people/` — nothing references it anymore.

### Add an external collaborator

The `collaborators:` list in the same file. `affiliation:` is optional:

```yaml
- name: Grace Hopper
  affiliation: Yale
```

### Edit the text of a section

Every block of prose on the page is one file in [`_sections/`](_sections/):

| File | Section |
|---|---|
| `10-about.md` | The intro paragraph under the carousel |
| `15-people.md` | The "submit a pull request" note (the lists come from `_data/people.yml`) |
| `20-activities.md` | Activities — **currently hidden**, see below |
| `30-join.md` | Join the lab! |
| `40-values.md` | Values Statement |

Edit the Markdown below the `---` frontmatter block. Bold is `**bold**`, a link is
`[text](https://url)`, a bullet is a leading `-`.

### Add, reorder, or hide a section

The frontmatter at the top of each `_sections/*.md` controls everything:

```yaml
---
title: Join the lab!    # the <h2> heading
anchor: join            # the URL fragment, i.e. /#join
order: 30               # position on the page; low numbers first
nav: true               # show a link in the top navbar
published: false        # hide the section entirely (omit this line to show it)
---
```

The navbar is generated from these same files, so a section and its nav link can never disagree.
Numbering leaves gaps (10, 15, 20, 30, 40) so you can slot something new in between without
renumbering everything.

**`20-activities.md` is the worked example**: it has `published: false`, so neither the section nor
its navbar link appears. Delete that one line and both come back.

To add a section, copy an existing file, rename it, and change the frontmatter. Nothing else needs
touching.

### Change the carousel photos

[`_data/carousel.yml`](_data/carousel.yml) — a list of images and their alt text. The first entry is
the slide shown on load. Indicator dots and arrows are generated from the list length. Please write
real alt text; it is what screen readers announce.

### Change the social links or the room number

[`_data/social.yml`](_data/social.yml). Leave `rel: me` on the Mastodon entry — that attribute is
what lets Mastodon verify the link back to this site.

---

## Previewing locally

You need Ruby (3.4 or newer). Once:

```sh
bundle install
```

Then, whenever you want to preview:

```sh
bundle exec jekyll serve
```

Open <http://localhost:4000>. Edits to `_data/`, `_sections/`, `_includes/`, and `style.css`
rebuild automatically — just refresh. **Edits to `_config.yml` require restarting the server.**

## How it deploys

Push to `main`. The [Pages workflow](.github/workflows/pages.yml) builds the site and publishes it;
there is no `_site/` directory in the repo and nothing to commit by hand.

Watch the run under the repo's **Actions** tab. If it's red, click into the failed step — it's
almost always a YAML syntax error in `_data/` or in a section's frontmatter. Indentation matters,
and a value containing a `:` must be quoted. Running `bundle exec jekyll build` locally reproduces
the same error faster than pushing again.

> [!NOTE]
> The repo's **Settings → Pages → Source** is set to **GitHub Actions**, and needs to stay that way.
> If it's ever switched to "Deploy from a branch", this workflow will keep going green while the
> live site silently serves something else.

## Repo map

```
_config.yml            Site title, description, URL. Restart the server after editing.
index.html             Assembles the page by looping over _sections/. Rarely needs editing.
_sections/             One Markdown file per section of the page.  <- most edits
_data/
  people.yml           Current members, external collaborators, alumni.  <- most edits
  carousel.yml         Homepage photo carousel.
  social.yml           Social links and lab location.
_layouts/default.html  The <html> skeleton.
_includes/             Reusable fragments: navbar, hero, carousel, people grid/lists.
style.css              Custom CSS layered on top of Bootstrap.
imgs/                  Photos, icons, banner, favicon.
*.pdf                  Informed-consent documents; linked from recruitment materials.
CNAME                  Custom domain. Do not delete.
```

Bootstrap 5 is loaded from a CDN in `_includes/head.html`. If you bump its version, you must also
update the two `integrity="sha384-..."` hashes there or the browser will refuse to load the files:

```sh
curl -sL https://cdn.jsdelivr.net/npm/bootstrap@VERSION/dist/css/bootstrap.min.css \
  | openssl dgst -sha384 -binary | openssl base64 -A
```
