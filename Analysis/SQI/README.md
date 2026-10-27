# Surface Quality Indicator (SQI) calculation

This folder documents how surface defect measurements are converted into the Surface Quality Indicator (SQI) used in the repeatability and capability analyses. It contains **630 surface records from 90 specimens**: 10 evaluation batches, nine build zones per batch, and seven surface views per specimen.

**`sqi_calculation.csv` combines the defect ratios and all surface-level SQI calculation steps in one table.** A separate `defect_ratio.csv` is no longer needed. The final score for each specimen is provided in `specimen_sqi.csv`.

## 1. Files and calculation flow

| File | Records | Contents |
| --- | ---: | --- |
| [defect_area_inputs.csv](defect_area_inputs.csv) | 630 | Part area, summed defect area, overlap and union area, and the defect ratio retained from the detection summary. |
| [surface_weights.csv](surface_weights.csv) | 7 | Physical surface areas in mm² and their normalized weights. |
| [sqi_calculation.csv](sqi_calculation.csv) | 630 | One row per specimen and surface: defect ratio, area, weight, information content, weighted information, and scaled SQI contribution. |
| [specimen_sqi.csv](specimen_sqi.csv) | 90 | One row per specimen: the total SQI across its seven surfaces. This is the input to [capability analysis](../Capability_analysis/). |

```text
Detection area measurements                Physical surface areas
defect_area_inputs.csv                     surface_weights.csv
             |                                      |
             +------------------+-------------------+
                                |
                     sqi_calculation.csv
          defect ratio -> information -> weighted contribution
                                |
                  sum the seven surface contributions
                                |
                       specimen_sqi.csv
                                |
                       Capability_analysis/
```

All CSV files contain numerical values, rather than executable spreadsheet formulas. The formulas and reproducible calculation below explain how those values are obtained.

## 2. Specimen and surface identifiers

- `specimen_id` combines the original batch number and build-zone number. For example, `21_1` is the specimen from original batch 21, build zone 1.
- `evaluation_batch` numbers the ten evaluation batches from **1 to 10**. These correspond to original batches **21 to 30**: `original_batch = evaluation_batch + 20`.
- `build_zone` is the specimen's position within a batch, numbered 1 to 9.
- `surface` is one of `front`, `back`, `side1`, `side2`, `top1`, `top2`, or `middle`.
- The unique key for a surface record is `(specimen_id, surface)`. Each specimen has exactly seven surface records.

The detection filenames use suffixes `F`, `B`, `S1`, `S2`, `U1`, and `U2` for the first six surfaces. The middle-surface filename has no surface suffix. For example, `21_1_F.txt` and the middle-surface `21_1.txt` both refer to specimen `21_1`. Match the specimen identifier **and** the `surface` column when joining files; the two CSV tables use different row orders.

These records describe the separate evaluation dataset. Its test images will be added later. The test dataset has no ground-truth annotations, and absence of annotations does not mean that a test image is defect-free.

## 3. Step 1: obtain the surface defect ratio

The defect ratio, DR, expresses defect area relative to the detected part area for a surface. It is stored as a fraction: **0.05 means 5%**.

The columns in `defect_area_inputs.csv` retain the numerical inputs from the supplied detection summary:

| Column | Meaning |
| --- | --- |
| `detection_file` | Identifier of the source detection output. |
| `surface` | Surface view used for matching to the SQI calculation table. |
| `part_area_normalized` | Detected part-region area in normalized image coordinates. |
| `defect_area_sum_normalized` | Sum of the detected defect areas before overlap correction. |
| `overlap_area_normalized` | Overlap-area correction recorded in the source calculation. |
| `union_defect_area_normalized` | Defect area after the source's overlap correction. |
| `defect_ratio_as_used` | The defect ratio actually passed to the SQI calculation. It matches `defect_ratio` in `sqi_calculation.csv`. |

The normalized image areas are dimensionless. They are distinct from the physical areas in mm² used for surface weighting in Step 3.

The supplied calculations use the following numerator conventions:

| Surfaces | Defect ratio used in the supplied results |
| --- | --- |
| `front`, `back`, `side1`, `side2`, `top1`, `top2` | `defect_area_sum_normalized / part_area_normalized` |
| `middle` | `union_defect_area_normalized / part_area_normalized` |

These conventions are retained to reproduce the supplied SQI results. Replacing the numerator with union area for every surface would change the results where overlaps occur. Use the supplied `defect_ratio` column to reproduce this release. The retained overlap and union columns allow the original inputs to be inspected.

## 4. Step 2: convert defect ratio to information content

For specimen *i* and surface *j*, the non-defective fraction is `1 − DRᵢⱼ`. Information content is calculated with a base-2 logarithm:

$$
I_{ij} = -\log_2(1-DR_{ij})
$$

This value is stored in `information_bits`.

- When DR = 0, information content is 0.
- As DR increases, information content increases, with a larger penalty as DR approaches 1.
- The calculation requires `0 ≤ DR < 1`. A missing ratio must not be replaced with zero. DR = 1 produces an unbounded value, and values outside this range cannot be used in this formula as finite real scores.

## 5. Step 3: weight each surface by its physical area

Each surface weight is its physical area divided by the total area of the seven surfaces:

$$
w_j = \frac{A_j}{\sum_{k=1}^{7} A_k}, \qquad \sum_{j=1}^{7} w_j = 1
$$

The following values reproduce the supplied calculation workbook. Areas are in mm². The displayed weights are rounded; use the full precision in `surface_weights.csv` for calculation.

| Surface | Area (mm²) | Weight |
| --- | ---: | ---: |
| front | 225 | 0.1357327883 |
| back | 225 | 0.1357327883 |
| side1 | 273.518530 | 0.1650019232 |
| side2 | 273.518530 | 0.1650019232 |
| top1 | 306.3619745 | 0.1848149557 |
| top2 | 306.3619745 | 0.1848149557 |
| middle | 47.907730 | 0.0289006657 |
| **Total** | **1657.668739** | **1.0000000000** |

The same seven weights are used for all 90 specimens. They depend on the physical surface geometry, not the apparent size of the detected part in an image.

## 6. Step 4: calculate surface contributions and specimen SQI

The area-weighted information content of each surface is:

$$
W_{ij} = w_j I_{ij}
$$

It is stored in `weighted_information_bits`. The contribution on the source SQI scale is:

$$
C_{ij} = 100 W_{ij}
$$

This value is stored in `scaled_contribution`. Finally, sum the seven contributions for each specimen:

$$
SQI_i = \sum_{j=1}^{7} C_{ij}
      = -100\sum_{j=1}^{7} w_j\log_2(1-DR_{ij})
$$

The factor **100** preserves the numerical scale used in the supplied analysis. SQI is a scaled information-based indicator, **not a defect percentage**. Lower values indicate less detected surface defect burden. Zero means all seven input defect ratios are zero; the score is not bounded above by 100.

Apply the logarithm to each surface ratio **before** weighting and summing. Applying the logarithm once to an average defect ratio generally gives a different result.

### Column definitions for the combined calculation table

| Column | Definition or formula | Unit |
| --- | --- | --- |
| `specimen_id` | Original batch and build-zone identifier. | Identifier |
| `evaluation_batch` | Evaluation batch 1–10, corresponding to original batch 21–30. | Identifier |
| `build_zone` | Build position 1–9. | Identifier |
| `surface` | One of the seven surface names. | Identifier |
| `defect_ratio` | DR retained from the detection summary. | Fraction |
| `area_mm2` | Physical area of this surface, A. | mm² |
| `weight` | Surface area / total area. | Dimensionless |
| `information_bits` | `-log2(1 - defect_ratio)`. | bits |
| `weighted_information_bits` | `weight * information_bits`. | bits |
| `scaled_contribution` | `100 * weighted_information_bits`. | SQI scale |

`specimen_sqi.csv` keeps the three specimen identifiers and the final `sqi` column. Its 90 values are also supplied in `../Capability_analysis/specimen_sqi.csv` for use in the capability calculation.

## 7. Worked example: specimen 21_1

For the front surface:

```text
Defect ratio = 0.00761496855352 / 0.5239225044270001
             = 0.014534532281350057
Information  = -log2(1 - 0.014534532281350057)
             = 0.02112277706753313 bits
Weight       = 225 / 1657.668739
             ≈ 0.13573278828659868
Contribution = 100 × 0.13573278828659868 × 0.02112277706753313
             ≈ 0.2867053427732496
```

The seven contributions are:

| Surface | Defect ratio | Information (bits) | Weight | SQI contribution |
| --- | ---: | ---: | ---: | ---: |
| front | 0.0145345323 | 0.0211227771 | 0.1357327883 | 0.2867053428 |
| back | 0.0029703032 | 0.0042916186 | 0.1357327883 | 0.0582513353 |
| side1 | 0 | 0 | 0.1650019232 | 0 |
| side2 | 0.0062208017 | 0.0090027511 | 0.1650019232 | 0.1485471248 |
| top1 | 0.0642067431 | 0.0957382618 | 0.1848149557 | 1.7693862607 |
| top2 | 0.0320679070 | 0.0470222587 | 0.1848149557 | 0.8690416650 |
| middle | 0.1400830980 | 0.2177308431 | 0.0289006657 | 0.6292566300 |
| **Specimen SQI** | | | | **3.7611883586** |

The CSV result is `3.761188358553998`. Values displayed here are rounded for readability; calculations should use the CSV precision. A stored `-0.0` is numerically equal to zero and can arise from the negative-logarithm calculation at DR = 0.

## 8. Reproduce the calculation

Run the following Python code from the repository root. It uses only the Python standard library. It recalculates every surface contribution from the combined table, checks the weights against physical areas, and compares the 90 specimen totals with the supplied results.

```python
import csv
import math
from collections import defaultdict
from pathlib import Path

base = Path("Analysis/SQI")

def read_csv(name):
    with (base / name).open(encoding="utf-8-sig", newline="") as stream:
        return list(csv.DictReader(stream))

surfaces = read_csv("surface_weights.csv")
areas = {row["surface"]: float(row["area_mm2"]) for row in surfaces}
total_area = sum(areas.values())
weights = {surface: area / total_area for surface, area in areas.items()}
assert len(areas) == 7
assert math.isclose(sum(weights.values()), 1.0, abs_tol=1e-12)
for row in surfaces:
    assert math.isclose(float(row["weight"]), weights[row["surface"]],
                        rel_tol=1e-12, abs_tol=1e-12)

rows = read_csv("sqi_calculation.csv")
totals = defaultdict(float)
seen = defaultdict(set)
assert len(rows) == 630
for row in rows:
    specimen, surface = row["specimen_id"], row["surface"]
    assert surface in weights and surface not in seen[specimen]
    seen[specimen].add(surface)
    dr = float(row["defect_ratio"])
    assert 0 <= dr < 1
    information = -math.log2(1 - dr)
    weighted = weights[surface] * information
    contribution = 100 * weighted
    expected = {
        "area_mm2": areas[surface],
        "weight": weights[surface],
        "information_bits": information,
        "weighted_information_bits": weighted,
        "scaled_contribution": contribution,
    }
    for column, value in expected.items():
        assert math.isclose(float(row[column]), value,
                            rel_tol=1e-12, abs_tol=1e-12)
    totals[specimen] += contribution

results = read_csv("specimen_sqi.csv")
assert len(results) == len(totals) == 90
assert {row["specimen_id"] for row in results} == set(totals)
max_error = 0.0
for row in results:
    specimen = row["specimen_id"]
    assert seen[specimen] == set(weights)
    error = abs(totals[specimen] - float(row["sqi"]))
    max_error = max(max_error, error)
assert max_error < 1e-10
print(f"Verified 630 surfaces and 90 specimen SQIs; max error = {max_error:.3g}")
```

## 9. Connection to capability analysis

Capability analysis uses the 90 specimen SQIs on the same scale. A defect-ratio threshold *t* is converted into an SQI upper specification limit using:

$$
USL(t) = -100\log_2(1-t)
$$

This equals the specimen SQI when all seven surfaces have the same defect ratio *t*, because their weights sum to one. For example, `t = 0.05` gives `USL ≈ 7.4000581444`. The sigma estimates and capability indices are described in the [capability analysis README](../Capability_analysis/README.md).

## 10. Numerical provenance and scope

The area measurements and defect ratios come from the supplied detection summary. Physical surface areas and specimen totals follow the supplied SQI calculation. This release retains their numerical conventions, including the surface-specific overlap treatment and the factor of 100. The original analysis workbooks are not included in this public package.

The combined table retains all 630 defect ratios without changing their values. Summing its seven contributions reproduces each of the 90 supplied specimen SQIs within `1e-10` absolute tolerance. This check verifies the numerical calculation from the supplied measurements; it does not rerun image detection or establish ground-truth annotation accuracy.
