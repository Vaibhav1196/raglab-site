# raglab_ site

Jekyll-powered library site for [ragforge](https://github.com/Vaibhav1196/hybrid-rag-lab) — a hybrid RAG framework built in public.

Two pages:
- **Home** (`/`) — live demos, code examples, build log, roadmap
- **Docs** (`/docs`) — quick start, installation, full API reference

All content lives in YAML files under `_data/`. Edit YAML → push → site rebuilds on GitHub Pages.

---

## Local development on macOS

### One-time setup

```bash
# 1. Install Ruby via Homebrew (skip if you already have Ruby 3.x+)
brew install ruby

# 2. Add Homebrew Ruby to your PATH (add these to ~/.zshrc)
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
echo 'export PATH="$(gem environment gemdir)/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# 3. Verify Ruby version (should be 3.x or 4.x, NOT 2.6)
ruby --version

# 4. Install Jekyll and Bundler
gem install jekyll bundler
```

### Run the site locally

```bash
cd ~/path/to/raglab-site

# If present
rm -f Gemfile.lock

# First time only (or after Gemfile changes)
bundle install

# If you get a webrick error, run this once:
bundle add webrick

# Start the local server
bundle exec jekyll serve

# → Open http://localhost:4000/raglab-site
```

Jekyll watches for file changes and auto-rebuilds. Just refresh your browser.

**Exception:** changes to `_config.yml` require a restart — hit `Ctrl+C` and run `bundle exec jekyll serve` again.

### Troubleshooting

| Problem | Fix |
|---------|-----|
| `cannot load such file -- csv` | Make sure your Gemfile has `gem "csv"` and `gem "base64"` and `gem "bigdecimal"`. Then `rm Gemfile.lock && bundle install` |
| `cannot load such file -- webrick` | Run `bundle add webrick` |
| CSS not loading / broken links | Check that `baseurl` in `_config.yml` matches your repo name (e.g., `/raglab-site`) |
| Old Ruby (2.6.x) | You're using macOS system Ruby. Install via Homebrew: `brew install ruby` and update your PATH |

---

## Deploy to GitHub Pages

1. Create a **public** GitHub repo (e.g., `raglab-site`)
2. Push this folder's contents to `main`
3. Repo **Settings → Pages → Source** → `main` branch, `/ (root)`
4. Wait ~60 seconds → live at `https://yourname.github.io/raglab-site`
5. Make sure `baseurl` in `_config.yml` matches your repo name

---

## File map

```
raglab-site/
├── _config.yml              ← site settings, hero text, social links
├── _data/
│   ├── quickstart.yml       ← installation + first code example
│   ├── api.yml              ← API reference (classes, methods, descriptions)
│   ├── examples.yml         ← code recipes shown on home page
│   ├── demos.yml            ← live demo cards (HF Spaces links)
│   ├── log.yml              ← build log entries
│   └── roadmap.yml          ← what's next for ragforge
├── _layouts/
│   └── default.html         ← base HTML wrapper (head, fonts, CSS link)
├── assets/css/
│   └── style.css            ← all styling (terminal aesthetic, syntax colors)
├── index.html               ← home page template (demos, examples, log, roadmap)
├── docs.html                ← docs page template (quickstart + API reference)
├── MAINTENANCE.md           ← detailed content maintenance guide
├── Gemfile                  ← Ruby dependencies for local preview
└── README.md                ← this file
```
