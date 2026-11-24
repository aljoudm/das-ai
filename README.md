# das-ai: the AI part of the DAS perimeter-security project (Hajj use case)

> **Copied from `aljoudm/full-das` at commit `79a0c35`. The original stays in full-das; this repository is an independent, intentionally duplicated copy of its AI part, so that it can be run on its own.**

Everything here is **SIMULATED or Brno-based**; nothing was measured on the FEBUS device yet. Every number in the AI summary is read from the result files (`scripts/make_ai_summary.py`).

## Parts
| Folder | What it is |
| --- | --- |
| `ai/` | Temporary event classifier (our re-implementation of the DASViT1D idea on the Brno data): window cutting (`brno_windows.py`), events (`brno_events.py`), features (`clf_common.py`), splits (`make_splits.py`, written once), training and the evaluation protocol (`clf_train.py`, `clf_protocol.py`) |
| `reid/` | Re-identification (DAS <-> camera) simulation and evaluation, following Yomna's protocol (`simulate.py`, `associate.py`, `evaluate.py`, `group_scene.py`, `plots.py`) |
| `tests/test_reid.py` | 7 unit tests of the re-identification code (including a check that its settings match the parameter table) |
| `scripts/make_ai_summary.py` | Builds `report/ai_summary.md` from the result files, nothing typed by hand |
| `results/classifier/`, `results/reid/` | The light results (metrics, confusion matrices, plots, grids) |
| `results/stage6_dual.csv`, `results/stage7_height_share.csv`, `results/stage9*/metrics_summary.csv` | Small inputs of the AI summary produced by the MATLAB simulation of full-das (frozen copies) |
| `docs/` | Classifier evaluation protocol and re-ID design protocol (Yomna), `brno_metadata.md` (every item of the protocol's "check in data" list) |
| `config/` | `ai_config.json`, `class_map.yaml`, `splits.json`, `splits_addendum.json`, and a read-only copy of the parameter table `parameters_reviewed.tsv` (read by `ai/clf_common.py` and `tests/test_reid.py`) |
| `report/` | `ai_summary.md`, `reid_summary.md`, `references_additions.bib` |

## Run on its own
Python 3.13 with `numpy`, `scipy`, `PyWavelets`, `librosa`, `scikit-learn`, `matplotlib` and `torch` (CPU is enough). All paths are relative to this folder.
```powershell
python -I tests/test_reid.py            # 7 tests, no data needed
python -I scripts/make_ai_summary.py    # rewrites report/ai_summary.md; identical to the file in full-das
python -I -m reid.evaluate tune         # re-ID: choose gate and margin on development scenes (see the docstring for the other commands)
python -I ai/clf_protocol.py 1 20       # classifier: needs the Brno data (below); window 1 s, 20 epochs
```
Re-running the classifier or the re-ID grid rewrites the files in `results/`; commit only if you mean to.

## Data (not in this repository)
The Brno event-classification dataset (about 44 GB extracted) lives **outside** the repo: `C:\data\das_brno` (`ai/brno_inventory.py` writes `C:/data/das_brno/inventory.json`; `ai/fetch_members.py` and `ai/zip_remote.py` fetch parts of the archive). Source: Tomasov, Zaviska, Dejdar, Klicnik, Horvath, Munster, *Comprehensive Dataset for Event Classification Using Distributed Acoustic Sensing (DAS) Systems*, figshare 10.6084/m9.figshare.27004732, licence CC BY (details in `docs/brno_metadata.md`). `.gitignore` blocks raw data, models and other large files.

## Licence and credits
* **Dataset:** CC BY (Tomasov et al.), cite as above. The dataset's own Python loader is GPL-3.0 and is **not used**; no code of the DASViT1D repository was copied.
* **Re-ID design and classifier protocol:** by Yomna (`docs/`); the re-ID design is hers and only her protocol is implemented in `reid/`.
* **Code in this repository:** no licence has been chosen yet; until the owner decides, all rights are reserved.

## Provenance
Files were copied (not moved) from `aljoudm/full-das` at commit `79a0c35`; paths in the code are relative to the repository root, so no code was changed. One difference by design: `ai/clf_protocol.py` records the commit of the repository it runs in, which is now the das-ai commit.
