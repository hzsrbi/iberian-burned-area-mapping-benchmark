# Accuracy vs. Operational Efficiency: Burned-Area Mapping in Iberia

**Satellite input configurations and machine-learning methods for mapping large burned areas in Spain and Portugal (2020–2024).**

This repository provides the research notebooks for comparing burned-area mapping approaches based on **Sentinel-2 (S2)**, **Sentinel-1 (S1)**, and several machine-learning methods: **Random Forest**, **U-Net**, **Siamese change-detection networks**, and **Prithvi** with frozen-backbone and LoRA adaptation. The workflow covers reference polygon preparation, Earth observation (EO) image collection and exporting, common sampling and event-level data splitting, model training, held-out-fire evaluation, and accuracy/efficiency summaries.

## Contents

| File | Purpose |
| --- | --- |
| [`notebooks/01_fire_extent_and_earth_observation.ipynb`](notebooks/01_fire_extent_and_earth_observation.ipynb) | Reads source fire polygons, constructs equal-area candidate unburned rings and study extents, selects Sentinel observations, submits Earth Engine exports, and reads downloaded rasters. |
| [`notebooks/02_burned_area_modelling_and_evaluation.ipynb`](notebooks/02_burned_area_modelling_and_evaluation.ipynb) | Creates/loads stratified fire-event samples, trains and evaluates RF, U-Net, Siamese and Prithvi configurations, generates full-extent predictions, and compiles comparison tables/figures. |
| [`requirements.txt`](requirements.txt) | Core geospatial, data science and modelling dependencies. |
| [`requirements-prithvi.txt`](requirements-prithvi.txt) | Additional optional Prithvi/TerraTorch dependencies. |
| [`docs/DATA_AND_REPRODUCIBILITY.md`](docs/DATA_AND_REPRODUCIBILITY.md) | Input data requirements, study design notes, and reproducibility limitations. |
| [`.gitignore`](.gitignore) | Excludes local data, credentials, model weights, and generated outputs from version control. |

## Research design

- **Study area:** selected large fires in Spain and Portugal, 2020–2024.
- **EO data:** Sentinel-2 surface reflectance and Sentinel-1 SAR (VV/VH) from Google Earth Engine.
- **Input configurations:** post-fire S2; pre/post S2; and, where supported by the method, pre/post S2 + S1.
- **Models:** Random Forest; U-Net; Siamese; Prithvi with frozen backbone or LoRA.
- **Evaluation:** held-out events, with consistent full-fire study extents and common comparison metrics. The original modelling notebook identifies `fire_01` and `fire_12` as test events.
- **Efficiency:** training time, prediction/inference time, and related resource measurements are reported where recorded by the workflow. Comparisons depend on the hardware and software environment.

These descriptions reflect the code organization and should not be taken as a guarantee that every listed experiment can execute in every runtime without adaptation.

## Quick start

1. Clone the repository and install the base dependencies in a clean Python environment:

   ```bash
   python -m venv .venv
   # On Windows: .venv\Scripts\activate
   # On macOS/Linux: source .venv/bin/activate
   python -m pip install -r requirements.txt
   ```

2. For **Prithvi** experiments, use a compatible GPU-enabled PyTorch runtime and additionally install the optional requirements (see the dependency notes below):

   ```bash
   python -m pip install -r requirements-prithvi.txt
   ```

   **Important:** GPU drivers, CUDA/PyTorch/TensorFlow compatibility, and model-checkpoint access are environment-specific. Avoid installing/upgrading major scientific packages inside an already running notebook.

3. Put your source fire polygon dataset at `PROJECT_DIR/spain-portugal-topfires-2020-2024.gpkg`, with layer `top_fires_2020_2024`, or adjust the filenames/layer in the first notebook. See [data requirements](docs/DATA_AND_REPRODUCIBILITY.md).

4. Set the data directory and Google Earth Engine project ID outside the notebook. Example (Linux/macOS):

   ```bash
   export FIRE_PROJECT_DIR="/path/to/your/fire-project"
   export EE_PROJECT_ID="your-earth-engine-enabled-cloud-project"
   ```

   On Windows PowerShell:

   ```powershell
   $env:FIRE_PROJECT_DIR = "D:\\research\\fire-project"
   $env:EE_PROJECT_ID = "your-earth-engine-enabled-cloud-project"
   ```

   In Google Colab, the notebooks use `/content/drive/MyDrive/Fire detection` as a **generic default example**; change it in the configuration cell or set `FIRE_PROJECT_DIR` as appropriate. Mounting Google Drive prompts for your own authorization; no Google login tokens are stored in the repository.

5. Open and run notebook **01**, download or synchronize the GeoTIFF exports from the specified Google Drive export folder (`eo_data`) into your local project EO data directory, and then run notebook **02**. Notebook **02** may need previously generated files or model checkpoints if you skip training steps.

6. The collection notebook expects a valid `EE_PROJECT_ID`. If the default placeholder `YOUR_GOOGLE_CLOUD_PROJECT_ID` is still present, replace it **before** Earth Engine initialization. Earth Engine authentication is interactive.

## Expected working data layout

```text
<your project directory>/
├── spain-portugal-topfires-2020-2024.gpkg  # Input; not included
├── fire_collection_areas.gpkg              # Prepared GIS layers
├── eo_data/                               # Exported S1/S2 GeoTIFFs + manifests
├── sampling/                              # Generated per-fire and combined samples
├── models/                                # Generated checkpoints (not in Git)
└── model_results/                         # Generated predictions, tables and figures
```

The notebook code specifies additional output names and intermediate folders. Consult each section before running all cells, especially on limited Colab storage.

## Dependency notes

The original workflow was developed with Google Colab and includes both TensorFlow/Keras and PyTorch/TerraTorch model families. A single pinned, tested environment for every architecture **is not supplied**; `requirements.txt` is a starting point rather than a lockfile. The original Prithvi environment referenced **TerraTorch 1.2.13**. For highest reproducibility, record and publish the exact working package versions, Python version, accelerator type, and pretrained-checkpoint identifiers from the runtime used for final experiments.

## Data, credentials and licensing

No fire input GeoPackage, EO raster imagery, checkpoint weights, saved notebook outputs, access tokens, or Google Earth Engine credentials are published with these notebooks. Do **not** commit a `.env` file, cloud service-account JSON, downloaded Drive cache, model weights, or any private field/reference data. See [data and reproducibility](docs/DATA_AND_REPRODUCIBILITY.md).

**License:** Not yet specified. Before publishing, choose a code license (for example MIT or BSD-3-Clause if compatible with all code and rights involved) and add an actual `LICENSE` file. Without a license, re-use permissions are limited by default.

## Citation

If these notebooks support a paper, add its complete bibliographic details and DOI here once available. Until then, cite the repository's stable release DOI, if one is created (for example through a Zenodo archive), rather than inventing publication details.

## Repository status

**Public research code — cleaned, not independently re-run.** Contributions and issue reports are welcome once the maintainer enables them on GitHub.
