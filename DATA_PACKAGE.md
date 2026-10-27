# AM Repeatability Research Data

## Folder structure

```text
Data/
  Train/
    images/
    annotations/
    inventory.csv
  Test/
    images/
Analysis/
  SQI/
  Capability_analysis/
```

Training data contain 1,260 surface images and one matching annotation file for each image. Images without a supplied label are defect-free, as confirmed by the author; their annotation files are empty. Three image identifiers have been normalized from hyphens to underscores to align with existing labels. The inventory preserves the filename mapping.

Test data refer to the separate evaluation batches 21-30. The test images folder is reserved for a later addition by the author. Test data have no ground-truth annotations; absence of annotations does not imply absence of defects. They are not populated with training or validation images.

Analysis contains only the SQI calculation chain (including derived defect ratios and intermediate contributions) and capability analysis. Available numerical evaluation records cover 90 specimens and 630 surfaces. Original analysis workbooks, cost models and exploratory analyses are excluded.

See each analysis folder's README for variables, formulas, units and retained numerical conventions. This repository is used to prepare the data package before its Zenodo deposit.

## Publication preparation

The full image and annotation files are organized in the local Data/Train folders. This GitHub repository documents the package and hosts the numerical analysis. The large image archive will be prepared from that same folder structure for the later Zenodo deposit. Test images will be added by the author.
