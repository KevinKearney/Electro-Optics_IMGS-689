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

`environment.yml` is a conda-format file. The instructions below are for conda:

```bash
conda env create -f environment.yml
conda activate physics
```

To pick up changes after editing `environment.yml`:

```bash
conda env update -f environment.yml --prune
```

### uv / venv (optional)

`environment.yml` is conda-specific -- neither `uv` nor Python's built-in `venv` reads it
directly, and there's no automated converter that reliably goes from `environment.yml` to a
`uv`/`venv` setup. Every package currently listed happens to also be available on PyPI, so
the same environment can be approximated by hand with either tool:

```bash
# uv
uv venv
uv pip install numpy scipy matplotlib pandas sympy jupyterlab notebook ipykernel ipywidgets h5py pyyaml tqdm astropy

# venv + pip
python -m venv .venv
source .venv/bin/activate
pip install numpy scipy matplotlib pandas sympy jupyterlab notebook ipykernel ipywidgets h5py pyyaml tqdm astropy
```

This list has to be kept in sync with `environment.yml` by hand if you add packages there --
conda is the source of truth for this project.
