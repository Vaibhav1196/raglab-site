# MAINTENANCE.md — raglab_ site content guide

This document describes how to keep the raglab_ site in sync with the
[hybrid-rag-lab](https://github.com/Vaibhav1196/hybrid-rag-lab) repository.

It serves two purposes:
1. **Manual reference** — step-by-step instructions for updating any section
2. **Codex instruction file** — an AI agent can read this file, inspect the
   hybrid-rag-lab repo for changes, and produce the correct YAML edits

---

## Architecture overview

The site is a Jekyll static site hosted on GitHub Pages. All content lives in
YAML data files under `_data/`. The HTML templates (`index.html`, `docs.html`)
read from these data files and render the site. You never need to edit HTML
or CSS to update content.

```
Site structure:
  index.html    → reads from demos.yml, examples.yml, log.yml, roadmap.yml
  docs.html     → reads from quickstart.yml, api.yml
  _config.yml   → site-wide settings (title, links, hero text)
```

The hybrid-rag-lab repo structure that maps to site content:

```
hybrid-rag-lab/
├── src/ragforge/           → api.yml (API reference)
│   ├── core/               → schemas, telemetry modules
│   ├── ingestion/          → loader, chunker modules
│   ├── retrieval/          → bm25, dense, fusion, reranking, pipeline modules
│   ├── generation/         → context, llm, pipeline modules
│   └── evaluation/         → retrieval eval, generation eval modules
├── scripts/                → quickstart.yml (CLI commands)
├── tests/                  → mentioned in api.yml if relevant
├── data/                   → sample documents and eval datasets
├── docs/                   → masterclass PDF, execution guide
└── pyproject.toml          → version, dependencies
```

---

## Trigger: when to update the site

Update the site whenever any of these happen in hybrid-rag-lab:

| What changed in hybrid-rag-lab | What to update on the site |
|-------------------------------|---------------------------|
| New module/class/function added to `src/ragforge/` | `_data/api.yml` — add new members |
| New module directory added (e.g., `ragforge.routing`) | `_data/api.yml` — add new module block |
| Existing function signature changed | `_data/api.yml` — update the member description |
| New CLI script added to `scripts/` | `_data/quickstart.yml` — add to `cli_commands` |
| New experiment run or finding | `_data/log.yml` — add entry at top |
| Bug fixed | `_data/log.yml` — add entry with type `fix` |
| Feature milestone reached | `_data/log.yml` — add entry with type `milestone` or `release` |
| New demo deployed to HF Spaces | `_data/demos.yml` — set status to `live`, add `demo_url` |
| New demo being built | `_data/demos.yml` — add block with status `wip` |
| New roadmap item identified | `_data/roadmap.yml` — add item |
| Roadmap item completed | `_data/roadmap.yml` — remove it; add to `log.yml` as release |
| Version bump in `pyproject.toml` | `_config.yml` — update hero comment line if version is mentioned |
| New useful code pattern worth showing | `_data/examples.yml` — add new example |

---

## File-by-file editing guide

### _data/demos.yml — Live demo cards

Shown on the home page as the first section visitors see.

**Add a new demo:**
```yaml
- title: "Your Demo Name"
  status: planned          # planned | wip | live
  description: "One sentence about what this demo lets people do."
  tags:
    - relevant-tech
    - another-tag
  demo_url: ""             # empty until deployed
```

**Promote a demo to live:**
```yaml
  status: live
  demo_url: "https://huggingface.co/spaces/Vaibhav1196/demo-name"
```

**Status color behavior:**
- `planned` → grey dot, grey "coming soon" button
- `wip` → amber dot, amber button
- `live` → green pulsing dot, green "→ try on HF Spaces" button

---

### _data/examples.yml — Code recipes (home page)

Syntax-highlighted Python examples shown on the home page.

**Add a new example:**
```yaml
- title: "Short descriptive title"
  description: "One sentence about what this shows."
  lang: python
  code_html: |
    <span class="syn-kw">from</span> <span class="syn-cls">ragforge.module</span> <span class="syn-kw">import</span> <span class="syn-cls">ClassName</span>

    result <span class="syn-op">=</span> <span class="syn-cls">ClassName</span>.<span class="syn-fn">method</span>(<span class="syn-str">"argument"</span>)
    <span class="syn-bi">print</span>(result)
```

**Syntax highlighting CSS classes:**
| Class | Color | Use for |
|-------|-------|---------|
| `syn-kw` | pink | `from`, `import`, `for`, `if`, `in`, `class`, `def`, `return` |
| `syn-cls` | cyan | class names: `HybridPipeline`, `RAGPipeline` |
| `syn-fn` | green | function/method names: `.search()`, `.answer()` |
| `syn-str` | yellow | strings: `"data/"`, `'query'` |
| `syn-num` | purple | numbers: `300`, `3`, `0.1` |
| `syn-bi` | orange | builtins: `print`, `len`, `range` |
| `syn-cm` | grey italic | comments: `# this is a comment` |
| `syn-op` | pink | operators: `=`, `==`, brackets when highlighting |
| `syn-pr` | green | bash prompt `$` |
| `syn-fl` | amber | bash flags: `--query`, `--top-k` |
| `syn-arg` | cyan | bash arguments, URLs |

---

### _data/log.yml — Build log

Newest entries go at the TOP of the file.

**Add a build log entry:**
```yaml
- date: "2026-04-15"
  type: experiment          # experiment | finding | fix | release | milestone
  message: "short description of what happened"
  link_text: "→ source"
  link_url: "https://github.com/Vaibhav1196/hybrid-rag-lab/blob/main/path/to/file"
```

**Type color behavior:**
- `experiment` → green
- `finding` → amber
- `fix` → red
- `release` → cyan
- `milestone` → purple

---

### _data/api.yml — API reference (docs page)

Organized by module. Each module lists its classes, functions, and protocols.

**Add a new member to an existing module:**
Find the module block and add to its `members` list:
```yaml
    - name: "NewClassName"
      kind: "class"           # class | dataclass | function | protocol
      description: "Short description of what it does."
```

**Add an entirely new module:**
```yaml
- module: "ragforge.new_module"
  description: "What this module is for."
  source: "src/ragforge/new_module/"
  members:
    - name: "SomeClass"
      kind: "class"
      description: "What it does."
    - name: "some_function(arg1, arg2)"
      kind: "function"
      description: "What it does."
```

**Kind badge colors:**
- `class` → cyan
- `dataclass` → green
- `function` → amber
- `protocol` → purple

---

### _data/quickstart.yml — Installation and first example (docs page)

**Update the install commands** if dependencies or setup steps change:
Edit the `install_html` field. Use `<span class="syn-pr">$</span>` for
the prompt, `<span class="syn-fl">--flag</span>` for flags, etc.

**Update CLI commands** when new scripts are added:
Add to the `cli_commands` list:
```yaml
  - cmd: "uv run python scripts/run_new_thing.py <span class=\"syn-fl\">--query</span> <span class=\"syn-str\">'test'</span>"
    label: "Description of what this does"
```

---

### _data/roadmap.yml — What's next

**Add a roadmap item:**
```yaml
- title: "Feature Name"
  horizon: next             # next | later | research
  description: "Short description."
```

**When a roadmap item is completed:**
1. Remove it from `roadmap.yml`
2. Add a `release` or `milestone` entry to `log.yml`
3. Add the new classes/functions to `api.yml`
4. Optionally add a code example to `examples.yml`

---

### _config.yml — Site-wide settings

Fields you might update:

```yaml
baseurl: "/raglab-site"         # must match your GitHub repo name
title: "raglab_"
tagline: "building RAG pipelines in public"
github_url: "https://github.com/Vaibhav1196/hybrid-rag-lab"
huggingface_url: "https://huggingface.co/yourname"
twitter_url: "https://twitter.com/yourname"
author_handle: "@vaibhav"
```

The `hero_lines` array controls the terminal animation on the home page.
Update it when you bump the ragforge version or want to change the intro.

---

## Workflow: new ragforge feature → site update

Step-by-step process when you add something to hybrid-rag-lab:

### 1. You implement a new feature

Example: you add `ragforge.retrieval.routing` with a `QueryRouter` class.

### 2. Update api.yml

Add the new module and its members:
```yaml
- module: "ragforge.retrieval.routing"
  description: "Smart query routing between sparse and dense retrievers."
  source: "src/ragforge/retrieval/routing.py"
  members:
    - name: "QueryRouter"
      kind: "class"
      description: "Routes keyword queries to BM25 and semantic queries to dense retrieval."
    - name: "classify_query(query)"
      kind: "function"
      description: "Returns 'keyword' or 'semantic' based on query characteristics."
```

### 3. Add a build log entry

At the TOP of `log.yml`:
```yaml
- date: "2026-04-15"
  type: release
  message: "query routing — automatic BM25/dense selection based on query type"
  link_text: "→ source"
  link_url: "https://github.com/Vaibhav1196/hybrid-rag-lab/blob/main/src/ragforge/retrieval/routing.py"
```

### 4. Update roadmap if applicable

If "Query routing" was listed in `roadmap.yml`, remove it.

### 5. Add a CLI command if there's a new script

In `quickstart.yml` → `cli_commands`:
```yaml
  - cmd: "uv run python scripts/run_routing.py <span class=\"syn-fl\">--query</span> <span class=\"syn-str\">'test query'</span>"
    label: "Query routing"
```

### 6. Optionally add an example

In `examples.yml`, add a new recipe showing the feature in action.

### 7. Push both repos

```bash
# Push ragforge changes
cd ~/hybrid-rag-lab
git add . && git commit -m "feat: query routing" && git push

# Push site changes
cd ~/raglab-site
git add . && git commit -m "docs: add query routing to API ref and log" && git push
```

Site auto-deploys via GitHub Pages within ~60 seconds.

---

## Workflow: new HuggingFace Spaces demo

### 1. Build the Gradio app

Create a directory with:
- `app.py` — Gradio interface wrapping ragforge
- `requirements.txt` — Python dependencies
- `README.md` — HF Spaces metadata (yaml frontmatter with sdk: gradio)
- `ragforge/` — copy of the ragforge package from `src/ragforge/`

### 2. Deploy to HF Spaces

Option A: Upload via the HF web interface at huggingface.co/new-space
Option B: Clone the Space repo and push via git

### 3. Update demos.yml

Change the demo status and add the URL:
```yaml
- title: "Hybrid Retrieval Comparison"
  status: live
  demo_url: "https://huggingface.co/spaces/Vaibhav1196/raglab-hybrid-retrieval"
```

### 4. Add a log entry

```yaml
- date: "2026-04-15"
  type: release
  message: "hybrid retrieval comparison demo live on HF Spaces"
  link_text: "→ demo"
  link_url: "https://huggingface.co/spaces/Vaibhav1196/raglab-hybrid-retrieval"
```

### 5. Push raglab-site

The home page now shows the demo with a green "live" badge and a clickable
"→ try on HF Spaces" button.

---

## Codex agent instructions

If an AI agent (Codex) is reading this file to automate site updates:

### What to inspect

1. Check `hybrid-rag-lab` repo for recent commits on `main`
2. For each commit, identify what changed:
   - New files in `src/ragforge/` → update `api.yml`
   - New files in `scripts/` → update `quickstart.yml` CLI commands
   - Changed `pyproject.toml` version → update `_config.yml` hero if needed
   - New eval datasets in `data/evals/` → optionally add to `log.yml`

### How to produce edits

1. Read the current YAML data files from `raglab-site/_data/`
2. Compare against the current state of `hybrid-rag-lab/src/ragforge/`
3. For new modules: parse the Python files for class names, function
   signatures, and docstrings → generate `api.yml` entries
4. For new scripts: read the argparse setup → generate `quickstart.yml`
   CLI command entries
5. Always add a `log.yml` entry for any substantive change
6. Output the modified YAML files as diffs or full replacements

### Rules

- `log.yml`: newest entries at the TOP
- `api.yml`: maintain module order matching the ragforge package structure
  (core → ingestion → retrieval → generation → evaluation)
- `examples.yml`: use `code_html` with syntax highlighting spans, not
  plain `code`. See the syntax class table above.
- `demos.yml`: only set `status: live` when a working HF Spaces URL exists
- `roadmap.yml`: remove items when completed, don't leave stale entries
- Never edit `index.html`, `docs.html`, or `style.css` for content changes
  — all content goes through `_data/` YAML files
- Preserve the existing YAML structure and field names exactly

### Validation

After generating edits, verify:
- All YAML files parse without errors
- No broken links (GitHub URLs point to real files)
- Every module in `src/ragforge/` that has public classes/functions is
  represented in `api.yml`
- Every script in `scripts/` is represented in `quickstart.yml` CLI commands
- `log.yml` entries have valid types: experiment | finding | fix | release | milestone
- `demos.yml` entries have valid statuses: planned | wip | live
- `roadmap.yml` entries have valid horizons: next | later | research

---

## What works with YAML-only edits vs what needs HTML changes

This is important to understand so you know the boundary of what's easy
(YAML edit, push, done) vs what requires template work.

### YAML-only — no HTML/CSS changes needed

These are all pure content changes. Edit the YAML file, push, the site
rebuilds correctly:

| Action | File to edit |
|--------|-------------|
| Add a new class/function to an existing module | `api.yml` — add to that module's `members` list |
| Add an entirely new ragforge submodule (e.g., `ragforge.routing`) | `api.yml` — add a new module block. The template loops over all modules, so it renders automatically. The docs page nav bar also updates automatically. |
| Add a new CLI script | `quickstart.yml` — add to `cli_commands` |
| Add a new code example / recipe | `examples.yml` — add a new block |
| Add a new demo card | `demos.yml` — add a new block |
| Change a demo from planned → wip → live | `demos.yml` — change `status` and add `demo_url` |
| Add a build log entry | `log.yml` — add at the top |
| Add or remove a roadmap item | `roadmap.yml` — add or delete a block |
| Change the hero terminal text | `_config.yml` → `hero_lines` |
| Update social links, tagline, author handle | `_config.yml` |
| Change the install instructions | `quickstart.yml` → `install_html` |
| Update the quick start code example | `quickstart.yml` → `example_html` |
| Reorder anything | Move blocks up/down within the YAML file. Render order matches file order. |

The key insight: the HTML templates use `{% for item in site.data.xxx %}`
loops. Any new item you add to a YAML list automatically appears in the
rendered page. No template changes needed.

### Needs HTML template changes

These are structural changes — adding a new *type* of section that doesn't
exist yet:

| Action | What to edit |
|--------|-------------|
| Add a new page (e.g., `/tutorials`, `/blog`) | Create a new `tutorials.html` file with Jekyll frontmatter. Add nav link in `index.html` and `docs.html` topbar. Create a `_data/tutorials.yml` for content. |
| Add a new section to the home page (e.g., "Benchmarks") | Edit `index.html` — add a new `<section>` block with a `{% for %}` loop reading from a new `_data/benchmarks.yml`. Create that YAML file. |
| Add a new section to the docs page | Edit `docs.html` — same approach. |
| Change the layout of an existing section (e.g., make demos a 3-column grid instead of 2) | Edit `assets/css/style.css` — change `.demos-grid` grid-template-columns. |
| Add a new syntax highlighting color | Edit `assets/css/style.css` — add a new `.syn-xxx` class. |
| Change fonts, colors, or the terminal aesthetic | Edit `assets/css/style.css` — modify `:root` variables. |

### How to add a new page (step by step)

If you ever need to add a page like `/tutorials` or `/benchmarks`:

1. Create `_data/tutorials.yml` with your content structure:
```yaml
- title: "Tutorial 1"
  description: "..."
  url: "..."
```

2. Create `tutorials.html` in the root:
```html
---
layout: default
permalink: /tutorials
---

<div class="topbar">
  <!-- copy topbar from index.html, add nav link for tutorials -->
</div>

<div class="container">
  <section class="section">
    <div class="section-head">
      <div class="comment-line">// learn ragforge step by step</div>
      <h2><span class="accent">$</span> tutorials</h2>
    </div>

    {% for tut in site.data.tutorials %}
    <div class="example-block">
      <h3>{{ tut.title }}</h3>
      <p>{{ tut.description }}</p>
    </div>
    {% endfor %}
  </section>
</div>
```

3. Add the nav link to `index.html` and `docs.html` topbar:
```html
<a href="{{ '/tutorials' | relative_url }}">tutorials</a>
```

4. Create `_data/tutorials.yml` and populate it.

After this one-time setup, adding new tutorials is back to being
YAML-only edits.

### Summary rule of thumb

> **Content changes = YAML only.**
> **Structural changes = HTML/CSS once, then YAML forever after.**

In practice, 95% of your updates will be YAML-only. The site structure
(home page with demos/examples/log/roadmap + docs page with quickstart/API)
covers the needs of a growing library. You'll only need HTML changes if you
want an entirely new type of section or page.

