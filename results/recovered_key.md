# Recovered Key — Byte-by-Byte Breakdown

This file is a template for recording the exact per-byte outcome of a CPA run against your own captured dataset, referenced from [`docs/10_Results.md`](../docs/10_Results.md#104-final-key-recovery-outcome). Fill in the `Recovered Value` and `Max |Correlation|` columns after running the attack in [`jupyter/ascon_cpa.ipynb`](../jupyter/ascon_cpa.ipynb).

| Byte Index | Leakage Model | Recovered Value | Max \|Correlation\| | Status |
|---:|---|---:|---:|---|
| 0 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 1 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 2 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 3 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 4 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 5 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 6 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 7 | y4 | `0x__` | 0.__ | ✅ Recovered |
| 8 | y2 | `0x__` | 0.__ | ✅ Recovered |
| 9 | y2 | `0x__` | 0.__ | ✅ Recovered |
| 10 | y2 | `0x__` | 0.__ | ✅ Recovered |
| 11 | y2 | `0x__` | 0.__ | ✅ Recovered |
| 12 | y2 | `0x__` | 0.__ | ✅ Recovered |
| 13 | y2 | `0x__` | 0.__ | ✅ Recovered |
| 14 | y2 | `0x__` | 0.__ | ❌ Not recovered |
| 15 | y2 | `0x__` | 0.__ | ❌ Not recovered |

**Recovery rate:** 14 / 16 (87.5%) — see [`docs/10_Results.md`](../docs/10_Results.md) and [`docs/11_Limitations.md`](../docs/11_Limitations.md) for discussion of the two unrecovered bytes.

> Byte indices 14–15 above are placeholders for "the two hardest bytes" discussed throughout the documentation — substitute the actual indices, values, and correlation scores from your own run.
