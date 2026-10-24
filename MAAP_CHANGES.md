# Changes from upstream

Upstream: https://github.com/arthur-e/MOD16, branch `master` at commit 27c11baac923410dbb3defcfd0f645cabb38f256 (2026-05-27), by K. Arthur Endsley (NTSG, University of Montana), MIT License.
The newest upstream release tag, v1.2.0 (c45e7a4), is 9 commits older. Between v1.2.0 and 27c11ba only the calibration code, its config, docstrings, the README and a new VNP16 prior changed; the forward-run notebook, `mod16/models.py` and `mod16/utils.py` are identical.

This copy starts from upstream's file tree at 27c11ba without its git history. It is unchanged except for the items below. The model code (`mod16/`), the BPLUT files, the tests and the LICENSE are untouched, so results do not change. Upstream has no `.github/` folder, so nothing was left out.

## Input data for the forward-run notebook

`notebooks/MOD16_Forward_Run_example.ipynb` needs `VNP16_MOD16_tower_site_latent_heat_and_drivers_v7.h5` (140 MiB, 317 FLUXNET tower sites, 2000 to 2019, CC-BY-SA-4.0) from Zenodo record 13988806 (https://doi.org/10.5281/zenodo.13988806; direct link https://zenodo.org/api/records/13988806/files/VNP16_MOD16_tower_site_latent_heat_and_drivers_v7.h5/content, no login needed). The notebook's introduction calls it `VIIRS_MOD16_...`; the file on Zenodo is named `VNP16_MOD16_...`. The MERRA-2 driver file used for MOD17 (`VIIRS_MOD16_MOD17_tower_site_drivers_v9.h5`) does not work here, since the notebook reads GEOS-IT drivers.

## 2026-10-24: driver file path is now a notebook parameter

The notebook had the input path hard-coded to the NTSG server (`/anx_lagr4/MODIS_VIIRS/calibration/...`), so it failed anywhere else. The `DRIVERS_HDF5` line now sits in its own cell tagged `parameters` (the papermill convention), and defaults to the Zenodo file name in the working directory. Run it with, for example:

```sh
papermill notebooks/MOD16_Forward_Run_example.ipynb out.ipynb -p DRIVERS_HDF5 /path/to/VNP16_MOD16_tower_site_latent_heat_and_drivers_v7.h5
```

No other cell changed, and the saved outputs were kept.

## 2026-10-24: notebook uses the standard `python3` kernel

The notebook was saved with a local Jupyter kernel named `mod17`, so papermill and fresh Jupyter installs stopped with `NoSuchKernel`. It now names the default `python3` kernel.

## 2026-10-24: matplotlib and pandas declared as dependencies

`mod16/utils.py` imports pandas (including `pandas._typing`, hence `pandas>=1.4.0`), and `mod16/calibration.py` and the notebook import matplotlib, but `pyproject.toml` listed neither. They used to arrive through other packages, and arviz 1.x no longer pulls in matplotlib, so the notebook failed with `ModuleNotFoundError: No module named 'matplotlib'`. Both are now in `dependencies`.

Not changed, for the record: `mod16/calibration.py` imports `ipdb` only in its interactive debug option, and `mod16/sensitivity.py` needs SALib, which the `[sensitivity]` extra already covers.

## 2026-10-24: README note on the notebook's input

One line in the README's Documentation section on setting `DRIVERS_HDF5`.

## Check

On 2026-10-24, in a fresh Python 3.12 environment: `pip install .` (which now pulls in matplotlib and pandas), `python tests/tests.py` (15 tests, OK), then the papermill command above with no `-k` option. The notebook ran to the end (about 50 s on a first run, 1.3 GB peak memory). Its outputs match the saved ones: `evaporation_soil(...)` = 5.083295223395212e-06, ET arrays of shape (7305, 317), and the same two plots and `invalid value encountered in power` warnings. Input md5: 15716f776afd56cb4a744d920f690591.

Versions come from `mod16.__version__` (still `v1.2.0`); this commit is tagged `v1.2.0.post1`.
