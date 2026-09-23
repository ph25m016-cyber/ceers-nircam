# Progress notes — CEERS NIRCam reduction (LRD project)

Working log for my reduction of CEERS NIRCam data with the
[CEERS pipeline](https://github.com/ceers/ceers-nircam) (Bagley et al.).
Raw data, the CRDS cache and pipeline products are kept locally and are
excluded from git (see `.gitignore`).

## Dataset

- Observation: `jw01345001001_02201_00001`, filter **F115W**
- All 8 short-wavelength detectors: `nrca1`–`nrca4`, `nrcb1`–`nrcb4`
- File list: `initial_8_manifest.txt`; raw files in `nircam1_f115w/uncals/`

## Environment

- Conda env: `bagley_stage1`
- Versions recorded in `nircam1_f115w/provenance/environment.txt`
  (JWST 3.0.0, CRDS 13.2.7, Astropy 7.2.0, Photutils 3.0.0)
- CRDS status before Stage 1: `nircam1_f115w/provenance/crds_status_before_stage1.txt`

## Status

| Step | Status |
|---|---|
| Download raw `_uncal` files (8 detectors) | Done |
| Local CRDS cache set up | Done |
| Stage 1 (`Detector1Pipeline`) + snowball masking | Done, all 8 detectors |
| 1/f noise removal (`remstriping.py`) | Done, all 8 detectors |
| Stage 2 (`image2`) | Not started |
| Stage 3 (tweakreg, mosaicking) | Not started |

Stage 1 outputs in `calibrated/` for each detector: `_ramp`, `_trapsfilled`,
`_rate`, plus the 1/f products `_rate_pre1f`, `_rate_horiz`, `_rate_vert`, `_rate_1fmask`.

## Changes to the pipeline code

- `snowball_wrapper.py`: `UNCALDIR` now points to `nircam1_f115w/uncals`.
- `snowball_run_pipeline.py`: the input path is now built as
  `<input_dir>/<dataset>_uncal.fits` (it previously dropped the suffix);
  added diagnostic prints (paths, whether the file exists, output directory
  contents) and counts of snowball-flagged pixels.
- `.gitignore`: excludes FITS files, the CRDS cache and output directories.

## Issues log

- **2026-09-14 — `nrca4` failed in the dark current step.**
  `ValueError: cannot reshape array of size 308278464 into shape (187,2048,2048)`
  while loading the dark reference file (`DarkModel`). The array is smaller
  than its header says, which points to a truncated or incomplete CRDS dark file.
  Full traceback: `logs/a4_error.log`.
  Re-run completed on 2026-09-21; `nrca4` outputs are now in `calibrated/`.

## Next steps

- Check the Stage 1 `rate` files (snowball and 1/f residuals).
- Run Stage 2 (`image2_1.7.2.asdf`, see `stage2.md`).
- Run Stage 3 (see `stage3.md`).
