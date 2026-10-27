# SQI calculation

The calculation proceeds from surface defect ratios to information content, area weighting and specimen-level SQI. The package contains 630 surface records and 90 specimen records. These numerical evaluation records are available even though the test images will be added later.

1. `defect_area_inputs.csv`: retained part/defect/overlap/union area inputs and defect ratios used by the source calculation.
2. `defect_ratio.csv`: the seven surface ratios for each specimen.
3. `surface_weights.csv`: actual surface areas in mm2 and normalized weights.
4. `sqi_calculation.csv`: information = -log2(1 - defect_ratio); weighted_information = weight * information; scaled_contribution = 100 * weighted_information.
5. `specimen_sqi.csv`: SQI = sum of the seven scaled contributions.

The factor 100 preserves the source numerical scale. The source surface areas and overlap handling are retained. Some source defect ratios use summed rather than overlap-subtracted defect area; these ratios have not been silently changed. The table of intermediate values makes the calculation inspectable. Source Excel workbooks and unrelated analyses are excluded.
