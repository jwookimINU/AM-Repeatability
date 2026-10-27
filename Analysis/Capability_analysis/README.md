# Capability analysis

Input: `specimen_sqi.csv`, 10 batches by 9 build zones (90 specimens).

`summary_statistics.csv` supplies the mean and three sigma estimates. Overall sigma is the sample standard deviation of all 90 values. Batch sigma is the average within-batch sample standard deviation divided by c4(9). Build-zone sigma is the average within-zone sample standard deviation divided by c4(10). c4(n) = sqrt(2/(n-1)) * Gamma(n/2) / Gamma((n-1)/2).

`capability_results.csv` evaluates defect-ratio thresholds 0.05 through 0.15, matching the manuscript's reported range. USL = -100*log2(1-threshold). The upper-only index is (USL - mean)/(3*sigma); no lower specification limit is imposed. Expected nonconformance assumes a normal distribution. Observed nonconforming counts use the same 90 specimens, so the count is identical across the three sigma-estimation scopes.

Source Excel workbooks, cost modeling, ARIMA and unrelated statistical explorations are excluded.
