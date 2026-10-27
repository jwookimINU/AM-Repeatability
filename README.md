# MEX Repeatability Research Data

Supporting data for a study of repeatability evaluation in polymer-based material extrusion using automated surface defect detection and process capability analysis.

## Study design

The development experiment comprises 20 batches with nine specimens per batch. Seven surface views per specimen yield 1,260 original images.

The repeatability evaluation comprises a separate ten batches with nine specimens per batch. The available evaluation data include surface-level defect ratios and specimen-level surface quality indicators.

## Data files

| File | Contents |
| --- | --- |
| `data/specimen_quality.csv` | 90 specimen-level quality indicator records |
| `data/surface_defect_ratios.csv` | 630 surface-level defect ratio records |
| `data/surface_weights_as_stored.csv` | Seven surface area inputs used in the supplied workbook |
| `data/image_label_inventory.csv` | Original image filenames and exact matching label filenames |

The image dataset and original analysis workbooks are prepared separately for Zenodo. Add the public Zenodo DOI link here after the deposit is published. No DOI has been assigned in this repository draft.

## Variables

- `evaluation_batch`: local evaluation batch index, 1-10.
- `build_zone`: specimen location on the build platform, 1-9.
- `specimen_id`: identifier retained from the source workbook, such as `21_1`.
- `surface`: workbook surface label: front, back, side1, side2, top1, top2 or middle.
- `defect_ratio_as_stored`: dimensionless surface defect ratio, exported without recalculation.
- `sqi_as_stored`: specimen-level surface quality indicator on the source workbook's numerical scale.
- `area_mm2_as_stored`: surface area input in square millimetres.

## Provenance and scope

The CSV files were exported from the supplied analysis workbooks. `specimen_quality.csv` comes from `Surface Quality Indicator.xlsx`, `Sheet2!A2:C91`; surface ratios come from `SAR!A4:J93`; area inputs come from `SAR!D1:J1`. Original workbooks are retained in the Zenodo preparation package.

This preparation draft contains supplied data and documentation. It does not include the final model weights, final model configuration, final training/validation split, or raw evaluation images. Exact label matching and agreement of the final manuscript with workbook scaling, area inputs and overlap treatment remain under author review. The original numerical values have been preserved.

## Citation and license

The dataset citation and open license will be added when the author finalizes the Zenodo release. Cite the dataset DOI once available. The existing draft upload identifier must not be used as a DOI.
