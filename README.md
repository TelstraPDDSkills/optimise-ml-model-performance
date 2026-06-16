# Training ML Models — Optimise Performance (JupyterLite demo)

An interactive, **zero-install** version of the *Training ML Models – How to Optimise
Performance* notebook. It runs entirely in the visitor's browser via
[JupyterLite](https://jupyterlite.readthedocs.io/) + [Pyodide](https://pyodide.org/)
(CPython compiled to WebAssembly). No Python, no `pip`, no server — just a link.

## ▶️ Live site

- Lab interface: **https://omarkhan00.github.io/demo-optimize-ml-model/**
- Open the notebook directly:
  **https://omarkhan00.github.io/demo-optimize-ml-model/lab/index.html?path=Training_ML_Models_Optimise_Performance.ipynb**

## How the packages work

Everything runs in the browser. Packages come from Pyodide's WebAssembly wheels:

| Package | How it loads |
|---|---|
| numpy, pandas, scipy, scikit-learn, matplotlib | Auto-loaded by Pyodide on first `import` |
| seaborn, optuna | Installed by the first cell: `%pip install -q seaborn optuna` |

The audience just opens the link and runs the cells top to bottom. The first run
downloads the wheels into the browser cache (a few seconds), then it's instant.

## ⚠️ One caveat — the PyCaret section (Section 7, Optional · Advanced)

PyCaret depends on compiled native libraries (`lightgbm`, `xgboost`, `catboost`,
`numba`) that have **no WebAssembly build**, so they cannot run in the browser.

- It has been **removed from the imports cell** so the rest of the notebook runs cleanly.
- Section 7 is framed as **Optional · Advanced**: the PyCaret code is **commented
  out** (so *Run All Cells* never errors) and paired with its **pre-computed output**
  in a markdown cell, so participants can follow the concept without running it.
- To run it live: download the notebook, `pip install pycaret`, uncomment, and run
  locally or in Colab.

## Reader & maintainer guides

- **[usage_instructions.md](usage_instructions.md)** — for participants: how to open and run the live notebook.
- **[setup_instructions.md](setup_instructions.md)** — for maintainers: how to publish any notebook to GitHub Pages this way.

## Repository layout

```
content/                         # notebooks served by the site
  Training_ML_Models_Optimise_Performance.ipynb
.github/workflows/deploy.yml     # builds + publishes to GitHub Pages on every push to main
requirements.txt                 # build toolchain (CI only)
```

## Deploying / updating

Pushing to `main` rebuilds and redeploys automatically. **One-time setup:** in the repo,
go to **Settings → Pages → Build and deployment → Source** and select **GitHub Actions**.

To edit the demo, change the notebook in `content/` and push — CI does the rest.

## Building locally (optional)

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lite build --contents content --output-dir dist
python -m http.server -d dist 8000   # then open http://localhost:8000
```
