# raglab_ site

Your Jekyll-powered landing page. Edit YAML files, push to GitHub, site updates automatically.

## Quick start

1. Create a **public** GitHub repo (e.g., `raglab-site`)
2. Push this folder's contents to it
3. Go to repo **Settings → Pages → Source** → select `main` branch, `/ (root)`
4. Wait ~60 seconds
5. Your site is live at `https://yourname.github.io/raglab-site`

**Important:** Make sure `baseurl` in `_config.yml` matches your repo name (e.g., `/raglab-site`). If you later use a custom domain or a `yourname.github.io` repo, set it to `""`.

## How to update content

### Add a new demo experiment

Edit `_data/demos.yml` — add a block like:

```yaml
- title: "Your Experiment Name"
  status: live          # live | wip | planned
  description: "What this experiment tests."
  tags:
    - some-tool
    - another-tool
  url: "https://huggingface.co/spaces/yourname/demo"
```

### Add a build log entry

Edit `_data/log.yml` — add at the TOP:

```yaml
- date: "2026-03-25"
  type: experiment      # experiment | finding | fix | release
  message: "what you did or discovered"
  link_text: "→ notes"
  link_url: "https://link-to-details"
```

### Update the architecture diagram

Edit `_data/architecture.yml` — modify the `ascii_art` block. Spacing matters since it renders as preformatted text.

### Update the "get involved" cards

Edit `_data/involved.yml`.

### Update site-wide settings

Edit `_config.yml` for:
- Site title and tagline
- Your GitHub / HuggingFace / Twitter links
- Hero terminal text
- Newsletter text

## File structure

```
raglab-site/
├── _config.yml              ← site settings, hero text, links
├── _data/
│   ├── demos.yml            ← demo experiment cards
│   ├── log.yml              ← build log entries
│   ├── architecture.yml     ← how-it-works diagram
│   └── involved.yml         ← get involved cards
├── _layouts/
│   └── default.html         ← base HTML wrapper
├── assets/
│   └── css/
│       └── style.css        ← all styling (terminal aesthetic)
├── index.html               ← main template (reads from _data/)
├── Gemfile                  ← GitHub Pages dependency
└── README.md                ← this file
```

## Local preview (optional)

If you want to preview before pushing:

```bash
bundle install
bundle exec jekyll serve
```

Then open `http://localhost:4000`

## What you never need to touch

- `index.html` — the template. It reads from your YAML files automatically.
- `_layouts/default.html` — the HTML wrapper.
- `assets/css/style.css` — unless you want to change the visual style.

All your day-to-day content edits happen in `_config.yml` and the `_data/` folder.
