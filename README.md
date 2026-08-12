# Portfolio site — Mohamed Saifudeen

A Jekyll site built for GitHub Pages. Dark, SOC/terminal-inspired theme; writeups and
projects are Jekyll **collections**, so adding new content is just adding a file — no
template edits needed.

## 1. Fill in your real info

Before doing anything else, open **`_config.yml`** and fill in the `author:` block
(email, GitHub, Medium, TryHackMe, LinkedIn, HackTheBox) and the `url:` field. These
flow automatically into the nav, footer, and contact page.

## 2. Run it locally

You'll need Ruby (3.x) installed.

```bash
gem install bundler
bundle install
bundle exec jekyll serve
```

Then open `http://localhost:4000`. The site rebuilds automatically as you edit files
(stop and restart if you change `_config.yml`).

> If `bundle install` fails on `github-pages` version conflicts, delete `Gemfile.lock`
> and run `bundle install` again — GitHub Pages updates that gem periodically.

## 3. Deploy to GitHub Pages

1. Push this repo to GitHub.
2. **Repo Settings → Pages → Source**: set to "Deploy from a branch", branch `main`
   (or `master`), folder `/ (root)`.
3. Wait a minute or two — your site will be live at
   `https://<your-username>.github.io/<repo-name>/` (or `https://<your-username>.github.io/`
   if the repo is named exactly `<your-username>.github.io`).
4. Update `url:` (and `baseurl:` if not using a user/org root repo) in `_config.yml` to
   match, then push again.

GitHub Pages builds Jekyll sites automatically on every push — no CI config needed.

## 4. Add a new writeup

Create a new file in `_writeups/`, e.g. `_writeups/my-new-room.md`:

```yaml
---
title: "Room Name"
platform: "TryHackMe"          # or "HackTheBox", etc.
category: "SOC"                 # e.g. Phishing Analysis, Malware Analysis, DFIR, Threat Intel
difficulty: "Medium"            # Easy / Medium / Hard — colors the card automatically
date: 2026-08-12
tags: [siem, log-analysis]
external_url: ""                 # optional link to a fuller writeup on Medium/GitHub
status: "complete"               # "draft" shows a TODO banner on the page; remove once done
summary: "One sentence for the card/preview."
---

Your write-up content goes here in normal markdown — headings, code blocks, images, tables.
```

Save it, and it automatically appears on `/writeups/`, sorted by date, no other changes
needed.

## 5. Add a new project

Same idea, in `_projects/`:

```yaml
---
title: "Project Name"
status: "In Progress"           # or "Complete"
stack: [Wazuh, Active Directory]
date: 2026-08-12
github_url: ""
writeup_url: ""
summary: "One sentence for the card/preview."
---

Project content in markdown.
```

## 6. Add images

Drop files in `assets/images/` and reference them in markdown:
`![alt text](/assets/images/filename.png)`

## Structure

```
_config.yml           site settings, nav data, your profile links
_layouts/              page templates (default, page, writeup, project)
_includes/              head, nav, footer partials
_writeups/              one file per writeup — auto-listed on /writeups/
_projects/              one file per project — auto-listed on /projects/
assets/css/main.scss    all styling (design tokens at the top)
index.html              homepage (hero + featured writeups/projects)
about.md, contact.md    static pages
```

## Still TODO

- [ ] Fill in real links/email in `_config.yml`
- [ ] Replace the three Boogeyman writeups (currently scaffolds — see the `TODO` notes
      inside each file in `_writeups/`) with your actual walkthroughs
- [ ] Fill in Enterprise 101 specifics (exact VM roles, screenshots, repo link) in
      `_projects/enterprise-101.md`
- [ ] Add a resume PDF to `assets/` and link it from the nav/contact page if you want one
- [ ] Fill in the "Background" section on the About page with your own origin story
- [ ] Optional: once you're speaking or writing outside your own site, a `_talks`
      collection can be added the same way as `_writeups`/`_projects`
