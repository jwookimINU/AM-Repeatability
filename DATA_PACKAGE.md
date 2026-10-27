# Data package structure

This repository is the preparation workspace for the research data deposit. Zenodo will host the fixed data archives used for the dataset citation.

## Files for the deposit

| Archive | Contents | Purpose |
| --- | --- | --- |
| `training_images_and_annotations.zip` | 1,260 surface images, 1,243 supplied annotation files, an image-to-label inventory and a dataset README | Surface defect detection development data |
| `analysis_core_data.zip` | Three numerical CSV files and a data dictionary/README | Underlying data for repeatability analysis |

The analysis deposit excludes the original Excel workbooks, intermediate worksheets, backup files and exploratory analyses.

## Core analysis data

| File | One row represents | Records | Key fields |
| --- | --- | --- | --- |
| `specimen_quality.csv` | One evaluated specimen | 90 | evaluation_batch, build_zone, sqi_as_stored |
| `surface_defect_ratios.csv` | One surface view of one evaluated specimen | 630 | specimen_id, evaluation_batch, build_zone, surface, defect_ratio_as_stored |
| `surface_weights_as_stored.csv` | One surface area input | 7 | surface, area_mm2_as_stored |

There are ten evaluation batches and nine build zones. Each specimen contributes seven surface views. CSV values preserve the existing numerical analysis values and scale. The archive README describes units and scaling.

## Training image data

The development experiment comprises 20 batches, nine specimens per batch, and seven surface images per specimen. The annotation files retain their supplied class IDs and coordinates. The inventory records exact filename matches. Three image-label pairs differ in punctuation and 17 images do not have a corresponding supplied annotation after accounting for these naming differences. No missing annotation has been fabricated.

The final model configuration, training/validation split and raw evaluation images are outside the currently available package. The dataset documentation must describe this scope without claiming full model retraining reproducibility.

## Release sequence

1. Organize the files and documentation in this repository.
2. Build the two archives to this specification; exclude source analysis workbooks.
3. Compare archive contents with the inventories and record checksums.
4. Upload the organized archives to the existing Zenodo draft and complete its metadata.
5. After release, add the registered dataset DOI and citation to this repository, the manuscript's Data Availability Statement and its reference list.

The reserved DOI is not yet a published data record. Repository preparation and draft uploads do not establish public data availability.
