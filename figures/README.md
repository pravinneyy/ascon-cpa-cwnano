# Figures

This folder holds the rendered plots referenced by the main [README](../README.md) and by [`docs/07_Trace_Capture.md`](../docs/07_Trace_Capture.md), [`docs/09_CPA_Attack.md`](../docs/09_CPA_Attack.md), and [`docs/10_Results.md`](../docs/10_Results.md).

Generate them by running [`jupyter/ascon_cpa.ipynb`](../jupyter/ascon_cpa.ipynb) end-to-end against your own captured trace dataset, then export each figure with `plt.savefig(...)` (instead of, or in addition to, `plt.show()`) using the exact filenames below so the markdown links resolve correctly on GitHub.

| Filename | Produced in | Description |
|---|---|---|
| `fig01_average_power_trace.png` | [docs/07](../docs/07_Trace_Capture.md#73-first-look-the-average-trace) | Mean of all 3,000 acquired traces |
| `fig02_variance_plot.png` | [docs/07](../docs/07_Trace_Capture.md#75-variance-analysis-finding-where-the-device-is-busy) | Per-sample variance across all traces |
| `fig03_random_traces.png` | [docs/07](../docs/07_Trace_Capture.md#74-a-qualitative-look-random-individual-traces) | 20 overlaid raw traces |
| `fig04_region_of_interest.png` | [docs/07](../docs/07_Trace_Capture.md#77-selecting-the-active-region-of-interest) | Variance plot with the 1,400–2,050 region highlighted |
| `fig05_attack_window.png` | [docs/09](../docs/09_CPA_Attack.md#98-selecting-the-final-attack-window) | Variance plot with the final 40-sample attack window highlighted |
| `fig06_correlation_heatmap_y4.png` | [docs/09](../docs/09_CPA_Attack.md#94-correlation-heatmaps) | 256 × samples correlation heatmap for the `y4` model |
| `fig07_correlation_heatmap_y2.png` | [docs/09](../docs/09_CPA_Attack.md#94-correlation-heatmaps) | 256 × samples correlation heatmap for the `y2` model |
| `fig08_correct_vs_wrong_keys.png` | [docs/09](../docs/09_CPA_Attack.md#95-the-correct-key-against-every-wrong-guess) | Correct key correlation trace vs. 255 incorrect guesses |
| `fig09_max_correlation_per_key_guess.png` | [docs/09](../docs/09_CPA_Attack.md#96-ranking-all-256-hypotheses) | Max correlation per key guess (0x00–0xFF) |
| `fig10_top20_key_guesses.png` | [docs/10](../docs/10_Results.md#103-top-key-candidates) | Bar chart of the top 20 candidate keys |
| `fig11_correlation_peak_location.png` | [docs/09](../docs/09_CPA_Attack.md#97-locating-the-precise-leakage-sample) | Marked correlation peak identifying the leakage sample |
| `fig12_key_recovery_summary.png` | [docs/10](../docs/10_Results.md#104-final-key-recovery-outcome) | Pie chart, recovered vs. unrecovered bytes |
| `fig13_traces_vs_recovery_rate.png` | [docs/10](../docs/10_Results.md#105-how-sensitive-is-this-to-trace-count) | Recovered bytes vs. trace count (500 → 3,000) |

