# Electro-Optics IMGS-689

Course work and independent-study materials, organized as one numbered subfolder per
project/topic.

## Layout

```
Electro-Optics_IMGS-689/
├── environment.yml        # shared conda environment for every project in this repo
├── README.md
├── .gitignore
├── 00_johnson-nyquist_noise/
│   ├── johnson_nyquist_tutorial.ipynb
│   └── johnson_nyquist_noise.pdf
└── 01_.../                # future projects follow the same NN_short-name convention
```

All projects share a single environment and a single git repository at this root. There is
no per-project `environment.yml` -- everything below depends on the one at the repository
root.

`CHEATSHEET.md` lives one level up, at `~/dev/CHEATSHEET.md` -- it's scoped to the whole
`~/dev` directory (every project you have, not just this one), so it isn't part of this
repository.

## Environment setup

Create the environment once, from the repository root:

```bash
conda env create -f environment.yml
conda activate physics
```

If this is the first time using this environment with Jupyter or Quarto on this machine,
register it as a kernel (a one-time step per machine -- it does **not** travel with the
environment or with any notebook file, and has to be redone on a fresh clone or a new
machine):

```bash
python -m ipykernel install --user --name physics --display-name "Python (physics)"
jupyter kernelspec list   # confirm "physics" now appears
```

To pick up changes after editing `environment.yml`:

```bash
conda env update -f environment.yml --prune
```
