# Fork & deploy to your own GitHub Pages

Run your own copy of this notebook as a zero-install site. No files to edit —
everything is already configured.

1. **Fork** this repo — click **Fork** (top-right).
2. In your fork, open the **Actions** tab → click
   **"I understand my workflows, go ahead and enable them"**
   *(forks disable Actions by default)*.
3. Go to **Settings → Pages → Build and deployment → Source** and select
   **GitHub Actions**.
4. Back in **Actions → "Deploy JupyterLite to GitHub Pages" → Run workflow**
   (branch `main`). Wait ~30s for the green check.
5. Your live site:
   **`https://<your-account-or-org>.github.io/optimise-ml-model-performance/`**

> ⚠️ Do step 3 **before** step 4 — otherwise the deploy job fails with
> `404 ... Ensure GitHub Pages has been enabled`. If that happens, enable Pages
> and re-run the workflow.

To change the content later: edit `content/…ipynb`, commit, and push — it
redeploys automatically.
