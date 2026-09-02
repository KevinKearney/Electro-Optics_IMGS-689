# Dev Environment Cheat Sheet

Recurring gotchas and commands across projects. Add entries only for things
likely to recur — not one-off fixes specific to a single document's content.

## Conda + Jupyter kernels

A conda environment is not automatically visible to Jupyter/Quarto as a
kernel. Creating the environment (`conda env create -f environment.yml`)
is not enough by itself — the kernel has to be registered separately,
once per environment, and this registration does **not** travel with the
environment.yml or any notebook file. On a fresh machine / fresh clone,
redo it.

```
conda activate <env-name>
python -m ipykernel install --user --name <env-name> --display-name "Python (<env-name>)"
```

Verify it's visible:

```
jupyter kernelspec list
```

If `<env-name>` doesn't show up:
- Confirm `ipykernel` is actually installed in the active env: `python -c "import ipykernel"`.
- If that import fails, `ipykernel` is missing from the environment despite
  being listed in `environment.yml` — reinstall or recreate the env.

A notebook's own metadata (`kernelspec.name`) just *names* which kernel it
wants — it doesn't register anything. Quarto's `jupyter: <name>` front-matter
key has the same relationship: it looks up that name in the kernelspec list
and fails loudly if it's not there (`ERROR: Jupyter kernel '<name>' not found`).

## Quarto rendering

```
quarto render notebook.ipynb --to html
quarto render notebook.ipynb --to pdf
quarto render notebook.ipynb            # renders every format listed in front matter
quarto preview notebook.ipynb           # live-reloading, defaults to html
```

`--execute` forces re-execution regardless of any cached/stored output —
useful for diagnosing whether Quarto ever actually ran the code, since a
silently-skipped execution and a genuinely empty notebook can look
identical in the rendered output (no code shown either way if
`echo: false`, and no error either way if nothing crashed).

PDF output requires a working LaTeX install:

```
quarto install tinytex   # one-time, if not already set up
```

Diagnostic checklist when expected output (figures, tables) is missing
from a render, cheapest check first:

1. `jupyter kernelspec list` — confirm the kernel Quarto's front matter
   names is actually registered (see above). This one has bitten us before.
2. Re-render with `--execute` and watch for an `Executing '<file>'` line in
   the console output — if it's missing, the code never ran.
3. Check for the intermediate output directory, e.g.
   `<file>_files/figure-pdf/` — if absent, execution didn't produce
   figures (or didn't happen); if present with image files inside, the
   problem is downstream in the LaTeX/pandoc assembly, not execution.

## Matplotlib under headless execution (Quarto / nbconvert / CI)

`plt.show()` only gets captured as notebook output when matplotlib is
running the Jupyter "inline" backend. If the environment's default
backend is an interactive one (`QtAgg`, `TkAgg`, etc. — check with
`python -c "import matplotlib; print(matplotlib.get_backend())"`),
`plt.show()` silently does nothing under headless execution (Quarto,
`nbconvert --execute`, CI): no error, no figure, just an empty output.

Fix by pinning the backend explicitly inside any notebook meant to be
executed headlessly, rather than relying on ambient environment state:

```python
%matplotlib inline
```

at the top of the notebook (first code cell, before any plotting). This
is independent of whatever interactive backend the environment defaults
to for normal Jupyter Lab use — no need to change that default globally.

## Quarto front matter for a hidden-code report

To suppress code from a rendered report globally (rather than tagging
every cell individually — per-cell tag support is inconsistent across
renderers), set execution options once in the YAML front matter:

```yaml
---
title: "..."
format:
  html:
    toc: true
    number-sections: true
  pdf:
    toc: true
    number-sections: true
jupyter: <kernel-name>
execute:
  echo: false
  warning: false
  message: false
---
```

Per-figure/table captions and cross-reference labels use `#|` comments at
the top of the code cell itself:

```python
#| label: fig-my-plot
#| fig-cap: "Caption text"
```
