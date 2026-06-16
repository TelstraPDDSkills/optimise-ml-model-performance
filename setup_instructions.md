# Setup: publish a Jupyter notebook to GitHub Pages (zero-install)

How to turn any `.ipynb` into an interactive site that runs **entirely in the
visitor's browser** — no Python, no server, no installs for the audience. This is
the exact recipe used to build this repo, written so you can repeat it for a new
notebook.

## How it works (30-second version)

The site is **JupyterLite** + **Pyodide**: CPython compiled to WebAssembly that
runs in the browser tab. GitHub Pages serves the static files; GitHub Actions
rebuilds them on every push. The catch: a package only works if it has a
**WebAssembly (Pyodide) build** — pure-Python and the major scientific stack do;
anything with compiled C/C++/Fortran extensions usually does not.

---

## Prerequisites

- A GitHub account and the `git` CLI (optionally the `gh` CLI).
- Python 3.10+ locally (only for the optional local test build).

---

## Step 1 — Create the project structure

```
my-notebook-site/
├── content/                     # notebooks the site will serve
│   └── My_Notebook.ipynb
├── requirements.txt             # build toolchain (CI only)
├── .github/workflows/deploy.yml # build + deploy to Pages
├── .gitignore
└── README.md
```

```bash
mkdir -p my-notebook-site/content my-notebook-site/.github/workflows
cp /path/to/My_Notebook.ipynb my-notebook-site/content/
```

## Step 2 — Sort out the packages

Pyodide **auto-loads** these on first `import` (nothing to do):
`numpy`, `pandas`, `scipy`, `scikit-learn`, `matplotlib`.

For packages **not** bundled but still pure-Python (e.g. `seaborn`, `optuna`),
add an install cell as the **first code cell** of the notebook:

```python
%pip install -q seaborn optuna
```

In JupyterLite this is intercepted by `piplite` and installs in-browser. It also
works unchanged in normal Jupyter/Colab.

For packages that **cannot** run in the browser (compiled native deps such as
`lightgbm`, `xgboost`, `catboost`, `numba`, and anything depending on them like
`pycaret`): remove their imports, and either drop those cells or keep them as
read-only content (comment the code, paste the pre-computed output into a markdown
cell, and tell readers to run that part locally).

> 🔎 **Check a package:** search the Pyodide packages list, or just try it in the
> local test build (Step 5). If `%pip install X` fails in the browser, it's not
> WASM-compatible.

## Step 3 — `requirements.txt` (build toolchain, runs in CI only)

```text
jupyterlite-core==0.6.4
jupyterlite-pyodide-kernel==0.6.1
jupyter-server==2.18.2
```

`jupyter-server` is required at build time to generate the file listing — the
build errors without it.

## Step 4 — `.github/workflows/deploy.yml`

```yaml
name: Deploy JupyterLite to GitHub Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: "pages"
  cancel-in-progress: false
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: python -m pip install -r requirements.txt
      - run: jupyter lite build --contents content --output-dir dist
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

Add a `.gitignore` so build output is never committed:

```text
dist/
_output/
.jupyterlite.doit.db
.venv/
.ipynb_checkpoints/
.DS_Store
```

## Step 5 — Build & test locally (optional but recommended)

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lite build --contents content --output-dir dist
python -m http.server -d dist 8000     # open http://localhost:8000
```

Open the site and **run every cell top to bottom** — this is where you confirm
the in-browser packages (e.g. `optuna`) actually work under Pyodide.

## Step 6 — Create the repo and push

```bash
cd my-notebook-site
git init -b main
git add -A
git commit -m "JupyterLite site"
git remote add origin https://github.com/<user>/<repo>.git
git push -u origin main
```

## Step 7 — Enable GitHub Pages (one-time, in the browser)

Repo **Settings ▸ Pages ▸ Build and deployment ▸ Source → "GitHub Actions"**.

> ⚠️ The first push runs the workflow, but the **deploy job fails until Pages is
> enabled** (`404 ... Ensure GitHub Pages has been enabled`). That's expected —
> enable the setting, then re-run:
>
> ```bash
> gh run rerun <run-id> --failed
> ```

## Step 8 — Done

Your site is live at:

- `https://<user>.github.io/<repo>/`
- Straight to a notebook:
  `https://<user>.github.io/<repo>/lab/index.html?path=My_Notebook.ipynb`

To update later: edit the notebook in `content/`, commit, and push — CI rebuilds
and redeploys automatically.

---

### Versions verified for this repo (2026-06-16)

`jupyterlite-core` 0.6.4 · `jupyterlite-pyodide-kernel` 0.6.1 ·
`jupyter-server` 2.18.2 · build Python 3.12.
