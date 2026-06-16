# How to run this notebook

Everything runs **inside your web browser**. You do **not** need to install
Python or any packages.

## Steps

1. **Open the link** in any modern browser (Chrome, Edge, Firefox, Safari):

   <https://omarkhan00.github.io/demo-optimize-ml-model/lab/index.html?path=Training_ML_Models_Optimise_Performance.ipynb>

2. **Wait a few seconds** for the page to load. It is starting Python inside your
   browser — this is normal.

3. **Run the first cell** (titled *"Install browser-compatible packages"*). Wait
   until it finishes before continuing.

4. **Run the rest of the cells top to bottom**, one at a time:
   - Click a cell, then press **Shift + Enter** to run it and move to the next.
   - Or, from the menu: **Run ▸ Run All Cells**.

5. The **first run downloads the packages** into your browser (a few seconds).
   After that, everything runs quickly.

## A note on the last section (Optional · Advanced)

The final **Auto Model Selection (PyCaret)** section is **optional** and does
**not** run in the browser — PyCaret needs libraries that don't work in
WebAssembly. Its code is left **commented out** and shown together with its
**pre-computed results**, so you can read and understand it without running it.

Want to run it for real? Download the notebook (**File ▸ Download**), then in a
local Jupyter or Google Colab: `pip install pycaret`, uncomment the code, and run.
